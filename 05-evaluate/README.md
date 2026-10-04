# Lab 5 — Evaluating Agent Quality

## Problem

The agent is deployed, secured, and observable — but none of that tells you whether it's actually giving good answers, solving customers' problems, or picking the right tools.

## If you don't solve this

"It works" and "it's good" quietly become the same sentence in most teams, because there's no number to separate them. An agent can be online, fast, and traced end to end while still being wrong a third of the time — hallucinating a product ID, picking the wrong tool, or technically answering without actually solving the customer's problem — and nobody notices until complaint volume rises or a customer escalates publicly. By then it's a reputation problem, not an engineering ticket, and there's no historical record of *when* quality started slipping because nobody was measuring it.

## How AWS solves it

AgentCore Evaluations runs LLM-as-judge scoring directly against live traffic, using any combination of 13 built-in evaluators (plus custom ones with your own scoring prompt) at a configurable sampling rate. Crucially, it doesn't just output a number — the judge's reasoning is retrievable per interaction, so a low score comes with an explanation pointing at the actual turn that went wrong.

## Approach

1. Configure online evaluation against the deployed agent, choosing which built-in evaluators to run (AgentCore ships 13; this lab uses 3: `GoalSuccessRate`, `Correctness`, `ToolSelectionAccuracy`) and a sampling rate (100% for the workshop, 10–20% suggested for real production traffic).
2. Deploy the eval config — it starts scoring new interactions automatically from that point on.
3. Generate varied test traffic on purpose (product queries, return-policy queries, warranty checks, multi-tool questions) so the evaluators have something real to judge.
4. Run an on-demand evaluation over the last day's traces and read back actual numbers, not vibes.
5. Drill into *why* a score landed where it did via the verbose per-interaction judge reasoning, not just the aggregate.

## Analysis

The sampling-rate decision (100% here vs. 10-20% suggested for production) is a real cost/coverage trade-off worth sitting with rather than copying blindly — 100% sampling is right for a workshop trying to prove the mechanism works, wrong for a cost-sensitive production system where a representative sample tells you the same trend for a fraction of the judge-model spend. The more important shift, though, is going from "quality is a vibe check done occasionally by a human" to "quality is a number tracked continuously by a judge model" — that's the difference between finding a regression in a retro and finding it the day it happened.

## Key takeaway

"The demo worked" and "the agent is good" are different questions, and only one of them has a number attached to it in most teams. A judge model that explains *why* it scored a response 0.5 instead of 1.0 — e.g. pointing at a specific invented product ID the user never mentioned — is the difference between a vague sense something's off and an actual root cause you can go fix.

## Tried and tested

Ran real evaluation against genuine test traffic and got real scores back (around 73% goal success, 84% correctness on my run — exact numbers will vary by traffic). Read the detailed per-interaction judge reasoning via `agentcore logs evals` to see the actual explanation behind a lower score.

## References

- Lab source: [AWS Workshop Studio — Lab 5: Evaluating Agent Quality](https://catalog.us-east-1.prod.workshops.aws/workshops/c770f35f-90a9-4e02-8985-4ef912bddb77/en-US/60-lab5-evals)
- [Amazon Bedrock AgentCore Evaluations docs](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluations.html)
