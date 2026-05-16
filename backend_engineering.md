---
name: backend-engineering
description: Backend architecture and implementation guidance for scalable, reliable systems. Use when an assistant is designing, implementing, reviewing, or improving backend services, APIs, jobs, repositories, validation layers, or supporting infrastructure and should preserve clear domain boundaries, data integrity, and operational safety across modular monoliths, microservices, and distributed systems.
---

# Backend Engineering

## Architectural Posture

Build backend systems for scalability, reliability, and maintainability.
Respect the existing architecture and patterns unless a strong architectural reason requires change.
Prefer feature-based organization when shaping or extending backend domains.
Keep shared infrastructure reusable and separate from feature modules.
Use a modular monolith shape unless the codebase clearly needs distributed services.
Do not proactively push microservices; use microservice patterns only when the user asks for them or the product constraints clearly demand them.
When microservices are requested or justified, define service boundaries around business capabilities, data ownership, deployment independence, and failure isolation.
Avoid microservices when a modular monolith can still provide clear boundaries with lower operational cost.
Use microservices to solve organizational/deployment scaling, isolated data ownership, independent scaling, or failure isolation problems; do not use them just to split files.
In microservices, plan API gateways, inter-service contracts, gRPC/REST/event contracts, message flows, observability, retries, timeouts, and eventual consistency from the start.
Keep feature modules as the home for feature-specific controllers, services, repositories, docs, tests, and jobs.
Keep reusable plumbing in `infra` modules.
Choose architecture based on product shape, team size, deployment needs, consistency requirements, operational maturity, and expected scale.

## Organize by Responsibility

Keep domain logic close to the feature it belongs to.
Place shared infrastructure in reusable modules.
Ensure features can depend on shared modules, but not the reverse.
Avoid scattering a feature's logic across unrelated directories.
Split oversized files or services when they accumulate multiple responsibilities.
Split by meaningful domain responsibility, not by tiny implementation details.
Prefer names that are clear without being noisy or over-explicit.
Use domain slices to avoid god files and services that mix unrelated workflows.

For feature modules in any backend stack, prefer:

```txt
src/<feature>/
  <feature>.<entrypoint>
  <feature>.<composition-root>
  dto/
  docs/
  test/
  repositories/
  services/
  jobs/
```

Avoid a generic facade service when the controller can depend clearly on domain services.

## Keep Layers Clean

Keep controllers and handlers thin.
Limit them to request parsing, validation, service calls, and response shaping.
Place business logic in services.
Keep services independent from HTTP concerns.
Use repositories as the only layer that talks directly to persistence.
Keep queries out of controllers and services.
Keep repositories focused on persistence access and avoid N+1 query patterns.
Prefer aggregated queries or explicit batched reads when returning stats or related data.
Keep external integrations behind client abstractions so storage, mail, payment, and provider clients are replaceable.
Apply the same layer discipline in any language or framework: handler/controller -> service/use case -> repository/gateway/client.

## Protect Domain Correctness

Enforce domain invariants in backend business logic, not in the frontend or request layer alone.
Validate all external input at the boundary before business logic runs.
Use runtime validation for requests, config, queue/job payloads, webhook payloads, message-broker events, file metadata, and external API responses that cross trust boundaries.
Use transactions when related writes must succeed or fail together.
Think through race conditions, duplicate submissions, concurrent jobs, and idempotency.
Avoid global mutable state.
Use database constraints for invariants that must survive concurrency.
Handle unique constraint races and transaction conflicts deliberately.
Understand isolation levels before relying on transaction behavior under concurrency.
Use consistent lock ordering and short transaction boundaries to reduce contention and deadlocks.
For security-sensitive flows, do the critical state change synchronously before returning.
Move only heavy or non-critical cleanup into background jobs.
Do not leave "later" placeholders for correctness, authorization, observability, idempotency, or cleanup when they are part of the current feature.

## Design for Operations

Keep external integrations in dedicated clients.
Handle errors, retries, and timeouts deliberately.
Use background jobs for heavy or asynchronous work.
Make jobs idempotent, retryable, and observable.
Use durable business idempotency keys for important transactional jobs; worker locks prevent overlap but do not prove business idempotency.
For important transactional work, prefer durable execution tracking such as a jobs table or outbox/inbox table with status, attempts, timestamps, error details, and a stable idempotency key.
Use queue job IDs and worker locks as execution controls, not as the only source of truth.
Let failed durable jobs remain inspectable after max retries.
Introduce caching only with a clear invalidation strategy.
Support liveness and readiness health checks, structured logging, request identifiers, graceful shutdown, and centralized error handling.
Close HTTP servers, consumers, database pools, cache clients, schedulers, and background workers safely on shutdown.
Add observability as part of initial service design, not as an afterthought.
Use the product's observability stack deliberately: APM/log platforms such as New Relic, or open stacks such as Prometheus, Grafana, Tempo, and Loki.
Emit useful logs, metrics, traces, and security/audit events without leaking sensitive data.
For distributed systems, design for retries, timeouts, circuit breakers, backpressure, graceful degradation, and eventual consistency.

## Queues, Brokers, and Async Boundaries

Use queues for background work and message brokers for async domain events, pub/sub, integration events, and cross-service communication.
Choose queue or broker semantics deliberately: work distribution, pub/sub fanout, ordered streams, delayed jobs, retry workflows, or event sourcing are different needs.
Do not add a broker just because it is available; use it when async decoupling, fanout, buffering, integration boundaries, or independent consumers are valuable.
Assume at-least-once delivery unless the system proves otherwise.
Make consumers idempotent and safe to retry.
Use dead-letter or failed-job inspection for important workflows.
Keep events small, versioned, and tied to stable business meaning.
Prefer explicit domain events over leaking database row shapes as event payloads.

## API and Data Discipline

Keep API responses consistent across endpoints.
Use predictable success and error shapes.
Paginate large collections.
Add indexes that match real query patterns while avoiding unnecessary write overhead.
Centralize configuration and never commit secrets.
Keep sensitive information out of logs.
Validate permanent external configuration at startup.
Treat external client outages as transient runtime failures, but missing external client configuration as startup failure.
Expose machine-readable API contracts in a way that is easy to download, such as a direct OpenAPI/JSON route or UI link.
Maintain API compatibility deliberately.
Version APIs or use additive response/request changes when clients may already depend on the current contract.
Avoid breaking response shapes, error codes, auth behavior, or webhook payloads without a migration plan.

## Schema and Migrations

Treat schema changes as production changes.
Prefer backward-compatible expand/contract migrations for live systems.
Separate schema changes, backfills, application reads, application writes, and cleanup when zero-downtime compatibility matters.
Plan rollback behavior before applying destructive migrations.
Avoid dropping columns, renaming columns, tightening nullability, or changing enum values until old application versions and data backfills are handled.
Use constraints and indexes to enforce invariants, but introduce them in a way that respects existing data volume and lock impact.

## Security and Auth

Treat auth as a system boundary, not only a login feature.
Understand session-based, token-based, and hybrid auth tradeoffs.
Use refresh-token rotation, secure cookies, server-side sessions, revocation, token versioning, and distributed invalidation when the system requires long-lived sessions.
Protect password reset, email verification, and OAuth/account-linking flows from enumeration, replay, and brute force.
Use defense in depth: input validation, output shaping, CORS strategy, CSRF protection where applicable, security headers, rate limits, lockouts, and audit events.
Design rate limits per threat model: IP limits, account limits, route limits, token buckets, sliding windows, lockouts, and trusted proxy handling all solve different problems.
Put authorization and resource ownership checks in the service/use-case layer, not only in route guards or middleware.
Use RBAC, ABAC, ownership checks, or policy objects based on the product's permission model.
Design webhook security with signature verification, timestamp/replay windows, idempotency keys, and durable processing.
Design secrets and config for rotation: do not bake permanent credentials into code, logs, builds, or long-lived client artifacts.

## Database and Performance

Model data lifecycle explicitly: soft deletes, audit logs, ownership, cascades, retention, and recovery.
Design indexes around real query patterns, pagination, joins, and ordering.
Start database performance work from the request, not only the individual query.
Measure query count, repeated queries, total database time, max query time, wait time, and connection usage per endpoint.
Profile slow queries after confirming the issue is inside the query itself.
Detect N+1 queries early; parallelizing N+1 queries may hide latency while increasing database pressure.
Avoid overfetching: select the fields and relation depth the use case actually needs.
Treat indexes as access-pattern tools, not generic speed switches.
For composite indexes, reason about column order and leftmost-prefix usage; do not rely on indexes that do not match the filter/sort access pattern.
Remember indexes are not free: they add write, storage, and memory overhead.
Watch connection pool pressure, throughput, transaction duration, and lock wait time.
Keep transaction boundaries short and intentional.
Understand isolation levels, contention, deadlocks, and retryable transaction failures.
Recognize compounding failure: repeated queries increase connection usage, which increases wait time, which lengthens transactions, which increases lock contention.
Prefer cursor pagination for large or frequently changing collections.
Use caching as a system layer with explicit consistency and invalidation rules.

## Search

Choose search based on product needs.
Use database-backed search for simple filtering, prefix search, small datasets, or operational simplicity.
Use dedicated search systems when ranking, typo tolerance, faceting, language analysis, high-volume indexing, or independent search scaling matters.
Keep indexing asynchronous and idempotent when search data is derived from primary data.

## File and Upload Pipelines

Design upload flows around trust boundaries, not a specific provider.
Authorize upload intent on the backend.
Generate storage keys or object identifiers server-side when ownership matters.
Use short-lived direct-upload credentials or stream through the backend based on file size, validation needs, and infrastructure constraints.
Require a backend confirm/finalize step before trusting uploaded data as application state.
Validate ownership, object identity, existence, content type, size, and other product-specific constraints before saving references.
Clean up abandoned, replaced, or deleted objects with idempotent background jobs.
Use multipart/resumable uploads only when file size and product experience justify the added complexity.

## Realtime and Integration

Prefer SSE over polling for one-way server-to-client updates when the client does not need bidirectional messaging.
Use WebSockets when bidirectional low-latency interaction is required.
Use polling only when simplicity, infrastructure limits, or product constraints justify it.
Use pub/sub or broker-backed fanout behind realtime gateways when horizontally scaled instances need to share events.
For horizontally scaled realtime systems, design presence, fanout, backpressure, and reconnect semantics deliberately.
For webhooks and payment flows, verify signatures, store idempotency keys, and treat external systems as at-least-once delivery sources.

## Testing and Quality

Test backend logic at multiple levels when appropriate.
Use unit tests for domain logic and services.
Use integration tests for persistence and API behavior.
Favor clarity, maintainability, and safe evolution over shortcuts that create technical debt.
Prefer a few meaningful tests over broad redundant tests.
Test edge cases that protect identity, security, cleanup, idempotency, and important state transitions.
