# Lab 1 — Building the Agent Prototype

## Problem

A model that can plan is not an agent. It needs tools to act, a way to run, and somewhere to live.

## If you don't solve this

Every "AI feature" ships as a chatbot wrapper: a prompt box bolted onto a single model call, with no way to actually *do* anything — look up an order, check a policy, search the web. The business ends up with something that sounds helpful and does nothing, and the gap between "looks like a demo" and "does real work" gets rediscovered the hard way, usually after a customer asks it to do something and it just makes an answer up. The customer-facing cost is a bot that confidently can't help; the engineering cost is realizing mid-project that "add a tool" means redesigning the whole request path, not configuring a setting.

## How AWS solves it

AgentCore separates "can the model reason" from "can it act" cleanly: a Strands agent gets `@tool`-decorated Python functions as its action surface, an MCP client for anything external (web search, here), and a managed Runtime that handles packaging, IAM, and scaling once deployed. The CLI (`agentcore create`) scaffolds all of this in one step instead of each piece being a separate integration project.

## Approach

1. Scaffold with the CLI (`agentcore create --framework Strands --model-provider Bedrock`) instead of hand-wiring a project.
2. Replace the generated sample tool with real domain tools (return policy lookup, product lookup) — a `@tool`-decorated Python function is the whole interface.
3. Pin the model explicitly (`model_id=...`) rather than trusting a default — every later lab depends on this being stable.
4. Test locally first (`agentcore dev`) before deploying — hot-reload, fast feedback, no cloud cost yet.
5. Deploy with one command (`agentcore deploy`) — infra (IAM, packaging, runtime config) is handled for you.

## Analysis

The real decision in this lab isn't "which tool framework" — it's accepting that a model and an agent are different things, and that the difference is exactly the part most teams skip when they're excited to ship a demo. The CLI's one-command deploy is deceptively significant: it means the IAM/packaging/runtime work that would normally be its own mini-project is handled, which removes the usual excuse for staying in "chatbot wrapper" mode because "doing it properly is too much infra work."

## Key takeaway

A model + tools is not yet a production agent — no memory, no shared tools, no security, no quality measurement. That's the deliberate baseline every later lab adds exactly one missing piece to. If you're evaluating "is this agent ready," check it against what labs 2–6 add, not against what Lab 1 alone gives you.

## Tried and tested

Ran all five steps myself end to end, including a real deploy. Confirmed working: `agentcore dev` for local iteration, `agentcore deploy` producing a live AgentCore Runtime endpoint invocable via `agentcore invoke`.

## References

- Lab source: [AWS Workshop Studio — Lab 1: Building the Agent Prototype](https://catalog.us-east-1.prod.workshops.aws/workshops/c770f35f-90a9-4e02-8985-4ef912bddb77/en-US/20-lab1-agent) — full code lives here, not reproduced in this repo
- [Amazon Bedrock AgentCore Runtime docs](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime.html)
- [Strands Agents SDK](https://strandsagents.com/)
