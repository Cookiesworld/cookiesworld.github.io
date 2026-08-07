---
layout: post
title: "What a Silent Heartbeat Taught Me About Debugging"
date: 2026-08-05
tags: [ai, debugging, openclaw, homelab]
---

I spent about a dollar today teaching myself a lesson I already knew in theory but hadn't really felt in practice: "it's not working" is never actually the bug. It's the symptom of three or four smaller, more specific things, and you don't fix any of them by guessing.

The dollar was spent on ai models using my Raspberry Pi 400 running [OpenClaw](https://openclaw.ai). A self-hosted AI agent gateway that talks to me over Telegram. I wanted a lightweight local model to handle its periodic "heartbeat" checks instead of paying for a cloud model every time. Simple enough: point the heartbeat config at a small model running locally via Ollama, done.

It wasn't done. Here's what "not working" actually turned out to be.

**It was a PATH problem, not a broken service.** Ollama was running fine — `systemctl status` said so. But the gateway couldn't reach it. Turned out a systemd override I'd applied weeks earlier had locked Ollama to the Docker bridge IP instead of localhost, on the assumption it only needed to be reachable from inside containers. It also needed to be reachable from the host process itself, which the override quietly broke. Nothing was crashing. Nothing was in the logs as an obvious error. It was just listening on the wrong address.

**It was a rate limit, not a bug.** Once local access worked, the free-tier fallback I'd set up (Groq) started failing with "request too large." Not a bug — a genuine mismatch. The routine background chatter OpenClaw sends with every turn (tool definitions, system prompt) runs to about 40,000 tokens before you've said anything. Groq's cheapest model caps you at 12,000 tokens _per minute_. The fix wasn't retrying, it was picking a model on their catalog with a higher cap.

**It was a missing config, not a dead end.** Even after the model started responding, nothing showed up in my Telegram. The heartbeat was working — generating real, if occasionally nonsensical, replies — and then discarding them, because I'd never told OpenClaw _where_ to send them. `target` defaults to none. No error. Just silence.

None of these were exotic. Each one was "check what's actually happening" rather than "assume and retry." The habit that saved the most time wasn't cleverness, it was splitting "is it reachable," "is it fast enough," and "is the request itself valid for this provider" into three separate questions instead of treating "broken" as one big undifferentiated problem.

The final setup routes through four working providers before it ever touches a paid one, and the local Pi model gets used when it can keep up and skipped when it can't. About $1 well spent, mostly on the education.
