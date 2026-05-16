---
name: frontend-logic-audit
description: Audit frontend applications for behavioral correctness, state reliability, and broken user flows. Use when an assistant should inspect component logic, async state transitions, validation, caching behavior, accessibility interactions, and client-side assumptions to find hidden bugs without redesigning the UI or auto-refactoring the code.
---

# Frontend Logic Audit

## Audit Goal

Audit the frontend for functional correctness, reliable state behavior, and broken user flows.
Focus on behavior rather than styling or formatting.
Do not automatically refactor the code.
Do not propose redesigns.

## What To Examine

Inspect component logic, UI state transitions, asynchronous flows, data fetching and caching, validation behavior, user interaction flows, accessibility behavior, and client-side security implications caused by logic flaws.
Look for duplicated state, stale closures, conflicting updates, fragile async sequencing, inconsistent query behavior, and components that rely on unstable assumptions.
Flag files that are excessively large or complex when that complexity increases correctness risk.
Check auth and upload flows for client assumptions that the backend does not guarantee.

## What To Look For

Check for:

- inconsistent loading, error, empty, and success states
- race conditions in requests or mutations
- stale cache usage or bad invalidation
- missing cache invalidation or optimistic update rollback after mutations
- duplicated or conflicting state ownership
- incorrect derived state
- broken form or modal flows
- validation gaps or inconsistent validation order
- broken keyboard interaction or focus management
- inaccessible error feedback
- fragile UI flows that depend on timing assumptions
- auth refresh races where multiple requests refresh simultaneously or overwrite newer credentials
- duplicate submissions caused by double-clicks, retry buttons, refreshes, or network retry behavior
- mutation retries without idempotency keys when duplicate side effects matter
- route protection that only hides UI without handling unauthorized data/API access
- access tokens persisted in unsafe long-lived browser storage when the app expects in-memory access tokens
- missing refresh-token-cookie handling in API clients
- direct-to-storage uploads that skip backend confirm
- UI treating a presigned upload as profile state before the backend accepts it
- stale generated API types or manually typed request payload drift
- polling used where SSE would provide simpler one-way updates
- realtime flows without reconnect, backoff, or missed-event recovery

## Output Requirements

For every issue found, provide:

- Issue Title
- Location in Code
- Description of the Problem
- Why It Is a Problem
- Possible Failure Scenario or Edge Case
- Severity Level (`Critical`, `High`, `Medium`, or `Low`)

## Reporting Rules

Present findings first, ordered by severity.
Summarize repeated patterns under `Systemic Issues` when they span multiple areas.
Keep the audit focused on correctness, state reliability, and real user-facing failure modes.
The goal is to expose where the interface can break before fixes are attempted.
