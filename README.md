# BeSA Cohort 10 — Week 1: Anatomy of an AI Agent

> **This is a personal learning log, not an AWS product or an AWS-endorsed resource.**
> All credit for the workshop content, lab design, and the AgentCore service itself belongs to **AWS** — specifically the official **"Getting Started with Amazon Bedrock AgentCore"** workshop (`c770f35f-90a9-4e02-8985-4ef912bddb77`) on [AWS Workshop Studio](https://catalog.workshops.aws/). This repo does not reproduce the workshop's code or instructions — it's my own digest of each lab: the problem it solves, the approach, and the one practical takeaway I'd want on hand later.
> **It does not replace the official AWS workshop.** For the actual steps and code, go to the source: [catalog.workshops.aws](https://catalog.workshops.aws/) and the specific lab links in each folder below.

Hands-on build from AWS Workshop Studio's **"Getting Started with Amazon Bedrock AgentCore"**, Labs 1–6 of 9, completed as part of BeSA Cohort 10's Week 1 ("Anatomy of an AI Agent").

One customer-support agent, built up one capability per lab. Each lab folder goes deeper than this table — problem, real-world cost of not solving it, how AWS solves it, approach, analysis, and a key takeaway:

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

## About BeSA

[Become a Solutions Architect (BeSA)](https://besa.techexpert.io/) is a free, community-run AWS programme taught by AWS volunteers. Cohort 10's theme was *Agentic AI: PoC to Production on AWS* — 6 weeks, a technical track and a behavioural/facilitation track each week. Week 1's technical session ("Anatomy of an AI Agent") was run by Ashish and Parna, with a fireside chat from Matt Wood (AWS Chief AI & Technology Officer).

Full field note (problem framing, AWS architecture diagrams, slide references) for Week 1: see the companion write-up at [cloudtoailearn.dev](https://cloudtoailearn.dev/) *(link to be added once published)*.

## Session recordings

- **Hands-on walkthrough (AgentCore labs):** [YouTube](https://www.youtube.com/watch?v=V46f5PvgoKY&list=PLX5aF9fnDyqE&index=8)
- **Technical track playlist:** [YouTube](https://www.youtube.com/playlist?list=PLaSlSRTym544)
- **Behavioural/facilitation track playlist:** [YouTube](https://www.youtube.com/playlist?list=PLJkCIS4Psec8)

Recorded and shared by the BeSA Batch 10 volunteer team. Credit for these recordings belongs to the volunteers who ran and captured the sessions, not to this repo.

## How this repo is organized

No source code is reproduced here — the official workshop is the place for that (linked from every lab folder). Each lab folder is a digest meant to be re-read later without redoing the lab: **Problem** (the technical gap), **If you don't solve this** (the real customer/business cost of leaving it unsolved), **How AWS solves it** (the mechanism), **Approach** (the steps taken), **Analysis** (the non-obvious design decision worth remembering), **Key takeaway**, **Tried and tested** (what was actually verified, not just read), and **References** (links to the official lab and the relevant AWS docs).
