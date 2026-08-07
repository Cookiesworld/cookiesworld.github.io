---
layout: post
title: "$0.015 a Post: Teaching OpenClaw to Post to X (formerly Twitter)"
date: 2026-08-06
tags: [ai, homelab, openclaw, security, automation]
---

## Starting with the boring option, on purpose

I run the self-hosted AI agent gateway [OpenClaw](https://openclaw.ai) on a Raspberry Pi 400. I've been tinkering with it for a while, setting up agents and seeing what I can use it for. It already handles my Telegram chat and its own periodic heartbeat checks. The obvious next step was letting it post somewhere on its own. X felt like the right place to start: high-volume, low-stakes, and forgiving of the odd mistake in a way a blog post or LinkedIn update isn't. If I was going to hand an agent more autonomy anywhere first, this was the place.

The easy path would have been a third-party bridge service. This would connect my X account through someone else's hosted OAuth flow, get posting working in under three minutes, move on. I said no to that on purpose. OpenClaw's whole setup so far has been about keeping the trust surface small: self-hosted, Docker-sandboxed, nothing running that doesn't need to. Routing my actual X account through another company's infrastructure for the sake of convenience I didn't need cut against that instinct. So: official X API, my own OAuth 1.0a app, credentials that never leave my own Pi.

## The $200 a month that wasn't

Before touching any code, I went looking for how much this would actually cost, and the first useful-looking result said X's API pricing starts at $200/month (about £148!) for write access. That's a real number that used to be true! X ran a fixed-tier system for a while. It just isn't true anymore. Checking X's actual current developer docs turned up something different: pay-per-usage pricing, no subscription. Posting costs $0.015 per request. At my planned cadence of three or four posts a week, that's about fifteen posts a month for roughly 25 cents (about 19p), not two hundred dollars.

It's a good reminder that the first search result being confident isn't the same as it being current. Pricing pages, especially for anything that's gone through a few ownership changes, age out fast.

## What "verified" turns out to mean

With the API side sorted, I went looking at OpenClaw's community skill registry [ClawHub](https://clawhub.ai) to see if someone had already built a clean X-posting integration rather than writing one from scratch. Two candidates looked promising on paper.

The first, a skill with the most installs of anything matching "twitter" in the registry, turned out to route my credentials through **ClawLink**, a third-party hosted OAuth broker. Functionally identical to the bridge service I'd already ruled out — just repackaged as a ClawHub listing with a clean automated security scan. The scan told me the markdown itself wasn't malicious. It didn't tell me the architecture matched what I'd actually decided I wanted.

The second was worse, and more interesting. ClawHub's own automated scan had flagged it **SUSPICIOUS**. Reading the actual skill file explained why: instead of using API credentials at all, it drove a logged-in browser session to scrape X's trending topics, generate "opinionated, engaging" hot takes, and publish them — with, by its own documentation, **no approval step required by default**. An agent instructed to be bold and go viral, posting from your account, unsupervised. That's the exact failure mode I'd been trying to design around from the start, sitting right there in a public registry with a name that made it sound like the sanctioned way to do this.

Neither fit. So I wrote it myself.

## A script small enough to actually read

The result is about 150 lines of plain Python — no dependencies beyond the standard library, OAuth 1.0a request-signing done by hand with `hmac` and `hashlib`. It does exactly one thing: post the text it's given, after asking `y/N` first. Nothing autonomous, nothing it decides on its own, nothing routed through infrastructure I don't control.

The first real test came back with a `402 Payment Required` and a message about depleted credits. My first instinct was that something in the signing was wrong. It wasn't — a 402 with a structured API error body actually means the opposite: the request was authenticated correctly and reached the right endpoint. If the OAuth signature had been wrong, X would have returned a 401. The real issue was simpler: a brand-new pay-per-usage developer account starts at zero balance, and even a perfectly valid request won't go through until it's funded.

Added a few dollars of credit, set a spending limit as a safety net, reran the exact same command. It posted.

## Where it actually stands

End to end: my own X Developer app, my own OAuth 1.0a keys, a script short enough to read start to finish in a couple of minutes, and a manual confirmation step before anything goes out. Nothing about how this works depends on trusting a third party, and nothing about it posts without me explicitly saying yes first.

Getting OpenClaw itself to trigger this instead of me running it by hand over SSH turned into its own, much longer story. This involved sandbox filesystem boundaries and an agent that, at one point, invented a safety feature that didn't actually exist and then tried to work around its own invention. That's a separate post. This part of the setup "does the posting actually work" on my terms is done. It cost about 25 cents (roughly 19p) a month to prove.
