---
name: backend-logic-audit
description: Audit backend systems for correctness, reliability, and hidden logic flaws. Use when an assistant should inspect business logic, validation, persistence flows, concurrency behavior, jobs, caching, and security-sensitive backend paths to identify failure scenarios without automatically rewriting the code.
---

# Backend Logic Audit

## Audit Goal

Audit backend behavior for functional correctness, data integrity, and reliability.
Focus on how the system behaves under normal and abnormal conditions.
Do not focus on style, formatting, or naming.
Do not automatically fix the code.
Do not recommend refactors unless they directly address a logic flaw.

## What To Examine

Inspect business logic, request flows, validation, data movement between layers, database interactions, state transitions, concurrency behavior, background jobs, caching, and security-sensitive logic paths.
Look for hidden coupling, duplicated domain rules, unsafe async workflows, and modules that bypass domain boundaries.
Flag files that are too large or complex to reason about safely when that complexity increases reliability risk.
Check repository queries for N+1 behavior, missing indexes, accidental full scans, and aggregate queries that should be batched.
Audit database performance from the request shape, not only from individual slow queries.
Look for query count, repeated queries, overfetching, connection pool pressure, transaction duration, lock waits, and contention.
Check API contracts, migrations, authorization, cache behavior, webhook processing, and queue retry behavior for compatibility and failure risks.

## What To Look For

Check for:

- incorrect assumptions
- missing edge-case handling
- inconsistent business rules
- partial writes or invalid state transitions
- missing transaction boundaries
- unsafe concurrent updates
- long transactions that hold connections or locks too long
- inconsistent lock ordering or deadlock-prone resource access
- incorrect transaction isolation assumptions
- non-idempotent jobs
- duplicate job execution risks
- missing rate limits or limits that do not match the threat model
- trusting proxy headers without an explicit trusted proxy configuration
- worker locks mistaken for durable business idempotency
- missing job ledger status or retry state for important transactional jobs
- cleanup jobs that can resend external side effects unexpectedly
- message consumers that are not idempotent under at-least-once delivery
- unversioned or oversized domain events
- missing liveness/readiness checks
- unsafe shutdown that can drop in-flight requests or jobs
- stale or inconsistent cache behavior
- validation gaps or bypasses
- missing object-level or resource-level authorization
- authorization checks that exist only in route guards while service methods can be reused unsafely
- migration changes that are not backward-compatible with old application versions
- destructive schema changes without backfill, rollout, or rollback plan
- API response/request shape changes that can break existing clients
- webhook signature, replay-window, or idempotency gaps
- queue retry storms caused by aggressive retry settings or missing backoff
- cache stampedes, unbounded cache keys, or cache entries without clear invalidation
- direct-upload flows that trust storage objects without backend confirmation
- storage confirm flows that fail to verify user namespace, key shape, existence, content type, size, and extension consistency
- broken authorization or privilege escalation paths
- user deletion flows that return before revoking active credentials or incrementing token version
- session invalidation gaps across horizontally scaled instances
- username uniqueness checks that can race and leak raw database errors

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
Summarize recurring patterns under `Systemic Issues` when they appear across modules.
Keep the audit centered on functional correctness and system behavior.
The goal is to expose where the backend can break before fixes are attempted.
