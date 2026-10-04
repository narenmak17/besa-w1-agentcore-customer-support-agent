# Lab 4 — Securing and Observing in Production

## Problem

By default the agent's endpoint is protected by AWS IAM — a caller needs signed AWS credentials. But IAM alone can't tell one customer from another without handing out AWS credentials to each of them, and there's no way yet to see *why* a given response came out the way it did.

## If you don't solve this

Without per-customer identity, you either hand out shared AWS credentials to every customer (a security incident waiting to happen) or you can't personalize anything, because the agent has no reliable way to know who it's actually talking to. Without observability, the failure mode is worse: an agent gives a wrong answer in production, a customer complains, and there's no trace, no log, nothing to point at except "it said something wrong" — no way to tell whether it was a bad tool call, a memory miss, or the model itself. Debugging becomes guesswork at exactly the moment it matters most: a live incident with a real customer affected.

## How AWS solves it

Two separate mechanisms, deliberately not conflated: session persistence/isolation (free, built into the Runtime, scoped by session-id) versus AgentCore Memory (durable, scoped by user-id, survives new sessions and runtime recycling) — Lab 4 proves the difference with a live three-call test rather than asserting it. Separately, AgentCore Runtime has been auto-instrumenting every call with OpenTelemetry since Lab 1 — traces and logs are already there, waiting, with zero extra setup. JWT-based auth (Cognito) replaces the all-or-nothing IAM default with real per-customer identity.

## Approach

1. Prove session continuity first: same session-id, same user → agent remembers mid-conversation for free (no Memory needed, just the live microVM).
2. Prove session isolation next: a brand-new session-id for a brand-new user → agent knows nothing (new microVM, nothing to recall).
3. Prove Memory bridges the two: a brand-new session-id for an *existing* user (from Lab 2) → agent still recalls their facts. Running all three back to back is what makes the distinction concrete instead of theoretical.
4. Pull real traces and logs with zero extra setup (`agentcore traces list`, `agentcore traces get <id>`, `agentcore logs --since 1h --query "warranty"`).
5. Add JWT-based auth (Cognito) to both the Runtime and the Gateway, replacing the free-for-all IAM-only default with per-customer identity.

## Analysis

The three-call test matters more than it looks like on paper, because the failure mode it guards against is a confident wrong assumption, not an obvious bug — a team that never runs this test can ship "memory" that's actually just session persistence and not find out until a customer complains in a second visit. On the observability side, the significant design choice is that instrumentation isn't opt-in: it's there from Lab 1, which means "add monitoring later" is never actually a true statement for an AgentCore agent, even if nobody's looked at a trace yet.

## Key takeaway

"It works in my session" and "it remembers this user" are different claims, and the only way to actually know which one you've built is to run all three tests back to back, not just the one that happens to work. Observability isn't something you bolt on later — it's been running since Lab 1.

## Tried and tested

Ran the three-call sequence (continuity → isolation → memory-bridges-the-gap) myself and got the expected result each time. Pulled a real trace with `agentcore traces get` and read the full span tree — prompt, memory retrieval, Gateway call, model response — for one actual request.

## References

- Lab source: [AWS Workshop Studio — Lab 4: Securing and Observing in Production](https://catalog.us-east-1.prod.workshops.aws/workshops/c770f35f-90a9-4e02-8985-4ef912bddb77/en-US/50-lab4-deploy)
- [Amazon Bedrock AgentCore Observability docs](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/observability.html)
- [Amazon Cognito user pools](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html)
