---
name: learning-walkthrough
description: Teach code and systems by transferring an executable mental model rather than giving an overview or file inventory. Use for completed implementation slices, focused request/event/job flows, architecture and data-flow explanations, returning to relearn existing work, or learning an unfamiliar codebase deeply enough to reason about, debug, and modify it safely.
---

# Learning Walkthrough

Teach the implementation until the learner can mentally execute it. Do not merely describe its structure.

The walkthrough succeeds when the learner can:

- predict the next step in the runtime flow
- explain which boundary owns each decision and why
- track data transformations and durable state changes
- identify the invariants and failure behavior
- locate the code involved in a bounded change or investigation
- narrate the implementation without relying on the walkthrough

## Select the learning context

Infer the context from the request and adapt the scope:

- **Completed slice:** reconstruct what was implemented from entry point to externally visible or durable outcome. Include the design decisions, proof from tests, operational dependencies, and safe extension points.
- **Focused flow:** follow one request, event, job, transaction, retry, or failure path through every meaningful boundary.
- **Re-entry or review:** rebuild the mental model for existing work, emphasizing how the pieces currently collaborate and what must be remembered before changing them.
- **Unfamiliar codebase:** establish only enough architecture to orient the learner, then teach representative real flows that make the abstractions concrete.

Do not force these contexts into separate rigid templates. Use the same execution-centered teaching method at the appropriate scale.

## Inspect before teaching

Read the relevant code, contracts, schema, configuration, migrations, and tests. Trace actual call paths instead of inferring behavior from filenames. When runtime evidence is available, use it to distinguish intended behavior from verified behavior.

Identify before writing:

- the responsibility being fulfilled
- the real entry point and terminal outcome
- the important boundaries and ownership decisions
- the state read or mutated
- the invariants, failure paths, and operational dependencies
- the evidence that proves the explanation

## Teach through execution

Begin with a compact mental model that gives the flow a purpose and shape. Treat it as orientation, never as the completed walkthrough.

Then execute the flow in order. At each meaningful step, explain:

1. what has just entered this boundary
2. the concrete function, handler, service, or component now in control
3. what decision or transformation it performs
4. why that responsibility belongs there
5. what it calls or emits next
6. what state, if any, changes
7. how failure changes the path or external result

Use concrete code references throughout. Introduce neighboring files only when they participate in the flow or clarify an ownership boundary. Explain abstractions at the moment they become operationally relevant.

## Build implementation ownership

Make the mechanics reconstructable, not just understandable in principle. Cover the relevant details among:

- request, command, event, and persistence data shapes
- validation and normalization order
- synchronous versus asynchronous boundaries
- dependency direction and adapter placement
- transactions, uniqueness, concurrency, idempotency, and retry behavior
- authentication, authorization, rate limits, and other security boundaries
- configuration, secrets, health, startup, and graceful failure
- protocol and error translation between layers or services
- tests that prove success, conflict, failure, rollback, and concurrency behavior
- the smallest safe path for modifying or debugging the implementation

Explain important code statements or algorithms closely when their ordering or semantics protect correctness. Do not paraphrase every line mechanically.

## Calibrate depth

Assume the learner understands general software-engineering concepts unless they ask for foundations. Do not reteach basic terminology merely because the walkthrough is educational.

Prefer practical design reasoning:

- why this layer owns the behavior
- why an alternative would violate a boundary or invariant
- what the code guarantees rather than merely intends
- what changes under concurrency, retries, partial failure, or multiple instances
- which complexity is required by the product and which is an implementation choice

If the learner signals uncertainty, zoom into the concrete path and data rather than replacing the explanation with a simpler overview.

## Use evidence as part of the lesson

Connect tests and runtime checks to the claims they establish. Explain what a passing test proves, what it does not prove, and which production risk it protects against.

For a completed slice, distinguish:

- implemented and verified behavior
- intentionally deferred behavior
- assumptions or operational requirements
- the next coherent slice

When a durable handoff is requested, preserve enough of this model for another session to resume without rediscovering the implementation. Keep progress logs concise; put the reconstructable runtime flow, decisions, invariants, verification commands, and next safe action in the handoff.

## Avoid

- stopping after an architecture overview
- using a directory tree or file list as the explanation
- narrating names without tracing control and data flow
- generic textbook explanations detached from the implementation
- assuming beginner knowledge or over-explaining established concepts
- turning the walkthrough into an unsolicited audit or refactor proposal
- claiming tests or runtime behavior that was not inspected or verified
- treating the happy path as the whole system

## Finish with a reconstruction check

End at the level appropriate to the request by consolidating:

- the runtime story in compact form
- the invariants and failure behavior worth retaining
- where to start when debugging or changing the flow

When useful, ask the learner to predict a failure path or modification point. Use their answer to locate remaining gaps, not as a quiz for its own sake.
