# Lab 6 — Building the Customer Interface

## Problem

Everything from Labs 1–5 is real, deployed, and working — and completely unreachable by an actual customer, who has neither a terminal nor a bearer token.

## If you don't solve this

Five labs of real infrastructure — memory, shared tools, security, observability, measured quality — sit behind a CLI only the engineering team can reach. The business value never materializes because no customer can get to it; the project reads as "done" internally while delivering zero actual customer impact. This is a surprisingly common failure mode: teams invest heavily in backend agent quality and treat the interface as an afterthought, then wonder why the project doesn't show up in any customer-facing metric.

## How AWS solves it

A thin Flask server handles only what a browser can't do safely on its own — authenticating against Cognito and handing over a scoped access token. Everything else, including the actual chat traffic, goes straight from the browser to the AgentCore REST API with that token in an `Authorization: Bearer` header. The backend agent (tools, memory, Gateway, evaluation) doesn't change at all — the frontend is additive, not a rewrite.

## Approach

1. Add a thin Flask server that authenticates against Cognito on startup, using the same `USER_PASSWORD_AUTH` flow already used from the CLI in Lab 4.
2. Serve a single chat page with the access token and runtime ARN injected into it.
3. Let the **browser** call the AgentCore REST API directly with an `Authorization: Bearer` header — Flask's job is just to authenticate and hand over a token, not to proxy every chat message.
4. Test the full stack end to end through the browser: product lookup, return policy, warranty check (via Gateway), and a "do you remember me?" check against Memory.
5. Test session reset deliberately — a new session should forget mid-conversation context but *still* recall long-term facts, which is the same distinction Lab 2/4 proved, now visible in a real UI instead of a CLI flag.

## Analysis

The architectural choice to let the browser call AgentCore directly (rather than proxying every message through Flask) is the part worth sitting with — it keeps the backend agent completely untouched by the UI work, which means the frontend team and the agent team can genuinely work in parallel without stepping on each other. The explicit workshop caveat about `USER_PASSWORD_AUTH` being a shortcut is also worth taking seriously rather than skimming past: it's the one place in this whole lab sequence where the "fast version" and the "production version" meaningfully diverge, and copying it wholesale into a real app would reintroduce exactly the kind of credential-handling risk Lab 4 spent its budget solving.

## Key takeaway

A solid backend isn't a finished product — it's finished when someone who isn't you can open a browser and use it. This lab's own auth shortcut (`USER_PASSWORD_AUTH` from the browser) is explicitly flagged by AWS as workshop-only — a real production frontend needs Cognito's Authorization Code flow with PKCE, or a backend-for-frontend that keeps tokens server-side.

## Tried and tested

Opened the chat UI in a browser and ran through all four test prompts (products, return policy, warranty, memory recall), then deliberately started a new session to confirm short-term context reset while long-term memory facts persisted — the same behavior proven via CLI in Labs 2 and 4, now visibly working through an actual UI.

## References

- Lab source: [AWS Workshop Studio — Lab 6: Building the Customer Interface](https://catalog.us-east-1.prod.workshops.aws/workshops/c770f35f-90a9-4e02-8985-4ef912bddb77/en-US/70-lab6-frontend)
- [AgentCore Runtime — invoking via the REST API](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-invoke.html)
- [Amazon Cognito — Authorization Code flow with PKCE](https://docs.aws.amazon.com/cognito/latest/developerguide/authorization-endpoint.html) (the production-correct auth pattern, vs. this lab's workshop shortcut)
