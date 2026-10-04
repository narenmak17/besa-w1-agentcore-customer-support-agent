# Lab 3 — Scaling Tools with Gateway

## Problem

Useful business logic usually already exists somewhere else — a Lambda another team owns, a REST API, an internal service — and it wasn't built with AI agents in mind.

## If you don't solve this

Two bad options, both common. Option one: duplicate the warranty/refund/inventory logic inside the agent's own codebase, which means two copies of business-critical logic drifting apart every time one team updates theirs and forgets to tell the other. Option two: refuse to give the agent that capability at all, so it answers "I can't check that, please call support" for exactly the questions a customer actually came to ask. Neither is acceptable at scale — one creates silent correctness bugs, the other makes the agent useless for anything beyond FAQ-level questions.

## How AWS solves it

AgentCore Gateway wraps an *existing*, unmodified backend — Lambda, a REST API Gateway stage, an OpenAPI service, a Smithy model, or another MCP server — behind one MCP-compatible endpoint. The tool description (name, natural-language purpose, input schema) is supplied separately as a schema file, so a Lambda that was never written with "AI agent" in mind becomes agent-callable without its owning team changing a line of their code.

## Approach

1. Identify the existing backend (this lab uses a Lambda, `workshop-warranty-check`, that another team already runs in production).
2. Write a tool *schema* — name, natural-language description, input shape — separately from the Lambda itself. The schema is what lets an agent reason about when and how to call something that has no built-in self-description.
3. Create a Gateway and register the Lambda as a target behind it (`agentcore add gateway`, `agentcore add gateway-target --type lambda-function-arn`).
4. Point the agent at the Gateway's single MCP endpoint, alongside its existing local tools and MCP clients — the agent discovers whatever's registered behind the Gateway automatically.
5. Deploy and test — the agent now calls a tool it doesn't own, through a URL, without any of that Lambda's code living in the agent's repo.

## Analysis

The schema-as-separate-artifact design is the part worth noticing: it decouples "describe this tool to an agent" from "own and maintain this tool's logic." That's an organizational answer as much as a technical one — the team that owns the Lambda keeps owning it, the team that owns the agent just writes a schema pointing at it. It also means one Gateway can front multiple target types at once, so the pattern scales past "one Lambda" to "every internal service this org has," without redesigning the agent each time.

## Key takeaway

Agent tools don't have to live in the agent's own codebase. A Gateway turns "copy their code into my agent" into "describe their service in one schema file." The Lambda itself was never touched.

## Tried and tested

Deployed the Gateway + Lambda target, then asked the agent to check a product's warranty status — confirmed the agent called the Gateway-routed Lambda correctly for both an expired and an active warranty, with no changes to the Lambda's own code.

## References

- Lab source: [AWS Workshop Studio — Lab 3: Scaling Tools with Gateway](https://catalog.us-east-1.prod.workshops.aws/workshops/c770f35f-90a9-4e02-8985-4ef912bddb77/en-US/40-lab3-gateway) — full code lives here, not reproduced in this repo
- [Amazon Bedrock AgentCore Gateway docs](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway.html)
- [Model Context Protocol (MCP) spec](https://modelcontextprotocol.io/)
