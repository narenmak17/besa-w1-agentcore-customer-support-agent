# Lab 2 — Add Memory to Your Agent

## Problem

The Lab 1 agent forgets everything the moment a session ends. A returning customer has to re-introduce themselves every time.

## If you don't solve this

A customer tells your support bot their order number, their preference for email over SMS, and the fact they already tried the obvious fix — then comes back an hour later, in a new session, and has to repeat all of it. Multiply that across every return visit and you've built something that actively wastes the customer's time compared to a human agent who'd at least glance at notes. Worse, teams often *think* they've solved this because testing within one long chat session looks fine — the gap only shows up in production, with real customers, across real session boundaries, which is a much more expensive place to discover it.

## How AWS solves it

AgentCore Memory splits into two durable strategies that don't depend on a live connection: SEMANTIC (facts — names, preferences, order details — extracted and made retrievable across sessions) and SUMMARIZATION (compressed conversation history for continuity). Both are namespace-scoped per user automatically, so one customer's facts can't leak into another's context by accident.

## Approach

1. Add a memory resource via CLI with two strategies: `SEMANTIC` and `SUMMARIZATION`.
2. Wire a memory session manager into the agent, with retrieval scoped per user (`/users/{actorId}/facts`) — isolation between users is automatic via namespace variables, not something you hand-write.
3. Switch from one global agent to a factory pattern — one agent per session/user combination, so memory stays scoped correctly.
4. Redeploy (memory is a cloud service; it does nothing until deployed).
5. Test with three calls, deliberately in this order: same session twice (proves nothing new), then a **brand-new session-id** for the same user (proves recall is coming from Memory, not from the live connection).

## Analysis

The dangerous failure mode here isn't "no memory" — it's "memory that only looks like it's working." A live session already feels like memory because the conversation state is right there in the process. The only test that actually distinguishes real Memory from a still-warm connection is forcing a session boundary, which is exactly the test most teams skip because it's inconvenient to set up, not because it's hard to understand.

## Key takeaway

Session persistence and long-term memory are not the same mechanism, and conflating them is the easiest way to think you've built memory when you've actually just built a long-lived connection. The one test that proves Memory is doing the work: start a new session-id and check if it still knows you.

## Tried and tested

Ran the three-call test myself — first message taught the agent a test identity's name and preference, a brand-new session (different UUID, same user header) recalled both correctly a couple of minutes later. Also let the runtime idle long enough to recycle its microVM and confirmed recall still worked — proof it's Memory, not just a still-warm process.

## References

- Lab source: [AWS Workshop Studio — Lab 2: Add Memory to Your Agent](https://catalog.us-east-1.prod.workshops.aws/workshops/c770f35f-90a9-4e02-8985-4ef912bddb77/en-US/30-lab2-memory) — full code lives here, not reproduced in this repo
- [Amazon Bedrock AgentCore Memory docs](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory.html)
