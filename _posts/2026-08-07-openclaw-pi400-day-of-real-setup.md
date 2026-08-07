---
layout: post
title: "Running My AI Assistant on a Raspberry Pi 400: A Day of Real Setup"
date: 2026-08-07
tags: [ai, homelab, openclaw, automation]
---

I run [OpenClaw](https://openclaw.ai/) — an open-source AI agent platform — on a Raspberry Pi 400. It's my personal automation hub: it lives in my workspace, reads my notes, and does things like posting to X and keeping my memory indexed. This is the story of a day spent turning it from "toy" into "actually useful," and the config mistakes I hit along the way.

## 1. Split-brain: two bots, one agent

The original setup had a single Telegram bot for everything — including me chatting with it directly. That means cron jobs and automation output would land in the same conversation I use for actual work. Noise.

The fix was a split-brain setup: two Telegram bots, one agent brain.

- `@Cookiesworldopenclaw_bot` — my chat lane. Human conversations.
- `@CookiesworldExecutorBot` — the automation sink. Cron jobs, scheduled posts, sub-agent output. It never clutters my main chat.

Under the hood, both route to the same agent (`host-executor`) with the same skills and credentials — just different entry points. One config patch: bind the executor Telegram account to the agent, fix the model to `deepseek/deepseek-v4-flash`, dry-run the patch first, then commit.

Smoke test: I DM'd the executor bot "post: testing executor." It posted to X. That single message proved routing, identity, skills, and API credentials all work through the new lane.

## 2. The day the memory index broke

The first casualty of a fresh setup: `openclaw memory index --force` failed with:

```
openai embeddings failed: 429 ... "You have no credits remaining."
```

Classic. OpenClaw defaults to OpenAI embeddings — and I have no OpenAI credits. The config never overrode it, so it was silently using a provider I don't pay for.

The fix: run embeddings locally instead. One config block, one model pull:

```json
"memorySearch": {
  "provider": "ollama",
  "model": "nomic-embed-text"
}
```

```bash
ollama pull nomic-embed-text   # 274MB
openclaw memory index --force --agent host-executor
```

Result: ` 29/29 files · 81 chunks → 30/30 files · 83 chunks.`. Fully local, zero API cost, and it works even if every cloud provider is down.

## 3. Hardening: fallbacks and security warnings

Two things I learned the hard way.

**Fallbacks.** If the primary embedding provider is down, the whole memory search fails closed. Adding a Gemini fallback means a dead Ollama process degrades gracefully instead of breaking recall:

```json
"memorySearch": {
  "provider": "ollama",
  "model": "nomic-embed-text",
  "fallback": "gemini",
  "sync": { "intervalMinutes": 60 }
}
```

The intervalMinutes is a safety net, not the primary mechanism — OpenClaw already watches memory files and re-indexes on change. The hourly sync just catches anything the watcher misses. No need to cargo-cult a cron job for this.

**Security warnings are worth reading.** OpenClaw flagged: "heartbeat delivery is configured while `agents.defaults.heartbeat.directPolicy` is unset." Translation: heartbeats could DM me, silently allowed by default. One line fixes it — explicitly block direct delivery until I actually want proactive pings:

```json
"heartbeat": {
  "model": "ollama/llama3.2:1b",
  "target": "last",
  "directPolicy": "block"
}
```

The model was moved from deepseek to local Ollama later in the day — section 4.

## 4. The silent money leak

Local embeddings were free, so I got greedy: I moved the heartbeat model onto local Ollama too (ollama/llama3.2:1b). Heartbeats are trivial — "reply HEARTBEAT_OK or raise an alert". A 1B model is overkill, and it costs nothing to run. Perfect, right?

The first heartbeat after the switch timed out. What happened:

LLM idle timeout (120s): no response from model → status 408
→ fallback to deepseek → candidate_succeeded

Look closely at that last line. The fallback worked — and that was the problem. My fallback chain quietly stepped in and ran the heartbeat on paid deepseek instead. I'd moved to local models to stop spending money, and the safety net I'd built was spending it for me without a peep.

The root cause was the hardware. It's a Pi 400 — 4GB of RAM, and mine sits at 44Mi free. Cold-loading a 1.5GB model into a starved box takes a while (my worst measured cold start: ~40s+), and OpenClaw's model idle watchdog is capped at 120 seconds by default. Cold start blew straight past it.

The fix is one config block that extends the watchdog for the local provider:

```json
"models": {
  "providers": {
    "ollama": {
      "api": "ollama",
      "timeoutSeconds": 300
    }
  }
}
```

Two and a half minutes of headroom for a cold load, bounded by the heartbeat's own run timeout so nothing can run away. Since then, heartbeats run free on Ollama.

The lesson: fallback chains are silent spenders. A fallback that "saves" you from a down primary will happily run on the paid provider every time the free one is slow — and you won't notice until the bill does. If your goal is zero-cost, the fallback needs to be the exception, not the silent default.

## What I'd tell my past self

1. **Defaults are opinions.** If you don't set a provider, OpenClaw assumes OpenAI — check your config before assuming anything is "free."
2. **Local-first beats cheap-cloud.** TThe Pi runs Ollama embeddings fine, once you give local models a proper timeout budget. The memory index doesn't care.
3. **Security warnings are config debt.** An unset policy is an implicit `allow`. Make the decision explicit — even if the decision is block everything for now.
4. **Smoke test the whole loop.** One DM → one X post proved the architecture end-to-end in seconds.
