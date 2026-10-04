# BeSA Cohort 10 — Week 1: Anatomy of an AI Agent

> **This is a personal learning log, not an AWS product or an AWS-endorsed resource.**
> All credit for the workshop content, lab design, and the AgentCore service itself belongs to **AWS** — specifically the official **"Getting Started with Amazon Bedrock AgentCore"** workshop (`c770f35f-90a9-4e02-8985-4ef912bddb77`) on [AWS Workshop Studio](https://catalog.workshops.aws/). This repo does not reproduce the workshop's code or instructions — it's my own write-up of each lab, in plain language, aimed at someone trying this kind of AWS/GenAI work for the first time.
> **It does not replace the official AWS workshop.** For the actual steps and code, go to the source: [catalog.workshops.aws](https://catalog.workshops.aws/) and the specific lab links in each folder below.

If you're new to building AI agents on AWS, start here. I went through this workshop myself and wrote up each lab the way I wish someone had explained it to me — what the problem actually is, why it matters in the real world, how AWS solves it, and what I actually tried myself (not just read about).

One customer-support agent, built up one new ability per lab. Each lab folder goes deeper than this table: the real-world problem, why it matters, how AWS solves it, the steps I took, a diagram, and the one thing worth remembering.

| Lab | What it adds | Real cost of skipping it |
|---|---|---|
| [1 · Prototype](01-agent-prototype/) | Tools + a managed Runtime, so the model can actually act | A chatbot wrapper that sounds helpful and does nothing |
| [2 · Memory](02-memory/) | Durable facts across sessions (SEMANTIC + SUMMARIZATION) | Customers repeat themselves every visit; feels worse than a human agent |
| [3 · Gateway](03-gateway/) | Existing backend (Lambda) exposed as a tool, unmodified | Duplicated business logic drifting out of sync, or an agent that can't do the thing the customer asked for |
| [4 · Secure & Observe](04-secure-observe/) | Per-customer identity (Cognito) + traces/logs that were already running | Shared credentials as a security risk, or a live incident with zero evidence to debug from |
| [5 · Evaluate](05-evaluate/) | LLM-judge scoring with per-interaction reasoning | "It works" and "it's good" silently treated as the same claim until a customer escalates |
| [6 · Frontend](06-frontend/) | A real browser chat UI calling the agent directly | Five labs of real infrastructure, reachable by nobody but the engineering team |

Each lab closes exactly one gap the last one opened, in the order production actually demands it: prototype → memory → shared tools → security/observability → proof of quality → a human-facing door.

**Not covered here:** Labs 7–9 of the full workshop (governing agent actions with policies, zero-code agents via AgentCore Harness, optimizing agent quality) — this repo covers Labs 1–6 only.

## Where this actually applies (not just customer support)

Every lab here is taught through one example — a customer-support agent — because that's how the AWS workshop teaches it. But the six layers are the reusable part, not that one example. A few places the same mechanisms solve a different, real problem:

- **Insurance claims triage** — the **Gateway** pattern from [Lab 3](03-gateway/) is the fit: most insurers run claims/policy logic on decades-old systems nobody's rewriting. Gateway exposes an existing system as a tool without touching its code — same mechanism as this repo's Lambda example, different industry.
- **Healthcare intake / triage** — the **Identity + Observability** pair from [Lab 4](04-secure-observe/) matters more than the model here: knowing exactly which patient an agent is acting for, with a full trace of every decision, is close to a compliance requirement, not a nice-to-have.
- **Where NOT to use this** — worth including because it's a real limit: loan eligibility screening on fixed income/credit-score rules belongs in a rule-based decision, not an agent. The original Week 1 material names this exact example. Knowing when *not* to reach for an agent is as much the point as the six layers themselves.

## About BeSA

[Become a Solutions Architect (BeSA)](https://besa.techexpert.io/) is a free, community-run AWS programme taught by AWS volunteers. Cohort 10's theme was *Agentic AI: PoC to Production on AWS* — 6 weeks, a technical track and a behavioural/facilitation track each week. Week 1's technical session ("Anatomy of an AI Agent") was run by Ashish and Parna, with a fireside chat from Matt Wood (AWS Chief AI & Technology Officer).

Full field note for Week 1 (the six-layer theory, slide references, and the industry-application section above in more depth): [linkedin-hub field note](http://127.0.0.1:7600/notes/2026-09-besa-programme/week-1) *(local viewer — a public mirror may follow; this repo is the publicly shareable version of the hands-on content either way)*.

## Session recordings

- **Hands-on walkthrough (AgentCore labs):** [YouTube](https://www.youtube.com/watch?v=V46f5PvgoKY&list=PLX5aF9fnDyqE&index=8)
- **Technical track playlist:** [YouTube](https://www.youtube.com/playlist?list=PLaSlSRTym544)
- **Behavioural/facilitation track playlist:** [YouTube](https://www.youtube.com/playlist?list=PLJkCIS4Psec8)

Recorded and shared by the BeSA Batch 10 volunteer team. Credit for these recordings belongs to the volunteers who ran and captured the sessions, not to this repo.

## How this repo is organized

No source code is reproduced here — the official workshop is the place for that (linked from every lab folder). Each lab folder follows the same shape, written for a first-timer: **the problem in plain terms**, **why it actually matters** (the real cost of not solving it), **what AWS gives you to fix it**, **what I actually did**, **a diagram of how it fits together**, **the one thing worth remembering**, **what I actually tested**, and **where to go for the real thing**.
