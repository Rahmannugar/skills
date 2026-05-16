---
name: learning-walkthrough
description: Explain an application, feature, system, or code flow so the user can deeply understand how it works. Use when the user wants a teaching-oriented walkthrough of architecture, system structure, data flow, control flow, logic, tradeoffs, or implementation details rather than an audit or rewrite.
---

# Learning Walkthrough

Use this skill when the user wants to understand a system, architecture, feature, or codebase well enough to work in it confidently.

This is a learning walkthrough, not an audit. The goal is understanding, not bug hunting.

## What to do

Start from the highest-value mental model first:

- what the system, feature, or flow is for
- which responsibilities are split across which parts of the code
- how the code structure reflects the architecture
- what product or operational constraints shaped the design

Then move gradually deeper:

1. Architecture
2. System structure
3. Data flow and control flow
4. Core logic
5. Implementation details that make behavior predictable

Tie explanations back to the code as you go. Show where each concept lives in the repository and how neighboring files/modules collaborate.
When the topic is not a codebase, tie explanations back to concrete components, services, data stores, protocols, or runtime flows instead.

## How to explain

Teach rather than document.

- Build intuition before diving into details.
- Prefer clear mental models over exhaustive file-by-file summaries.
- Bring in related files only when they help explain the main topic.
- Explain why the implementation is shaped this way, not just what each part does.
- Explain tradeoffs, not only mechanics.
- Call out important assumptions, tradeoffs, and edge cases when they affect how the system behaves.
- Explain production implications such as scaling, security, failure modes, data consistency, observability, and operational cost when relevant.

When useful, explain:

- where requests/events enter the system
- how data is transformed or validated
- where important decisions are made
- how state changes over time
- what other modules or services a component depends on
- why a component belongs in a domain/feature module or shared infrastructure layer
- what is synchronous and security-critical versus what is deferred to jobs
- how durable job status, retries, and idempotency fit into the flow
- how direct-to-storage upload and confirm flows protect application state
- how rate limits, sessions, token invalidation, health checks, and graceful shutdown fit into production behavior
- how query count, overfetching, indexes, pools, transactions, and locks shape database performance under load
- when SSE, WebSockets, polling, queues, brokers, or pub/sub are appropriate
- what can be modified safely next and what requires extra caution

## Working style

Before explaining, inspect the relevant code paths instead of guessing.

Prefer a walkthrough that feels like guided onboarding:

- start broad
- zoom into the important path
- connect abstractions to concrete files/functions
- end with the practical understanding the user would need to modify the code safely
- end with the safest next modification path when the user is preparing to change the system

If the topic is large, organize the explanation around the main flow instead of trying to cover every file.

## Avoid

- turning the walkthrough into a code audit
- leading with bugs, risks, or refactor advice
- focusing on formatting or naming commentary unless it affects understanding
- mechanically enumerating every file without building a mental model

## Output shape

Adapt to the user's question, but usually aim for:

1. Core idea and purpose
2. Main building blocks
3. End-to-end flow through the code
4. Important implementation details, assumptions, and tradeoffs
5. Safe next steps or modification guidance when useful

Use code references throughout so the user can follow along in the repository.
