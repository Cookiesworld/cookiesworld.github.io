---
layout: post
title: "673 to Pass: The AZ-204 Retake, and an Unexpected Expert Cert"
date: 2026-07-31
tags: [azure, certification, career, dotnet]
---

## The score that stuck with me

673 out of 700.

That was my AZ-204 result a few months back — 27 points short of a pass. I wrote about it [at the time](/2026/07/09/az-204-near-miss.html), because the near-miss taught me more than a clean pass would have. A score report that close doesn't feel like failure so much as a very precise to-do list. It told me exactly where the gaps were: user authentication and authorisation, Azure Functions, and message-based solutions.

Looking back at that list now, the pattern is obvious. Those are the areas where twenty-plus years of building things on .NET helps the least. The exam isn't testing whether you can build a working system — I can, and have, many times over. It's testing whether you know which of four similar-looking Azure services Microsoft wants you to reach for, and why, under exam conditions, with no IDE to lean on.

## Closing the gap

I went through the QA MAZ204 course properly this time rather than relying on broad revision, and spent the last stretch doing targeted practice on exactly those three areas — managed identity vs. service principal, Durable Function patterns, Service Bus topics and dead-letter queues, delegated vs. application permissions. Small, specific gaps, closed one at a time.

Today I sat the retake. [Passed](https://learn.microsoft.com/api/credentials/share/en-gb/cookiesworld/876D4315B70F5EC9?sharingId=5E8F56AF1E06BC9C).

## The certification I didn't know I still had

While logging into Microsoft Learn to check the result, I found something I wasn't expecting: [**Microsoft Certified: DevOps Engineer Expert**](https://learn.microsoft.com/api/credentials/share/en-gb/cookiesworld/CC6B0944FD4B3E39?sharingId=5E8F56AF1E06BC9C), dated the same day. I'd sat the exams out of order at some point and genuinely thought the prerequisite window had lapsed — turns out it hadn't. Two certifications, one day, more by accident of timing than by design.

It's a neat bit of symmetry, though. DevOps Engineer Expert sits above the Associate-level exams and assumes exactly the kind of breadth AZ-204 tests — identity, messaging, serverless compute — stitched together across the wider delivery lifecycle. Fixing the AZ-204 gap turned out to be foundational for the Expert-level material too, not incidental to it.

## The bit worth remembering

I nearly didn't post about the 673. It's not the comfortable number to share. But the response to that post — and the fact that I now have a proper "here's how it resolved" story to tell — is a decent argument for writing about the attempt, not just the outcome.

Sometimes the detour is the shortcut.
