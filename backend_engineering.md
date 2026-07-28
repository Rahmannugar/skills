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

## Service Boundaries

Define service boundaries around:
- business capabilities
- ownership
- data ownership
- deployment independence

Avoid splitting services around:
- database tables
- CRUD resources
- technical layers

A service should own its data and business rules.

Cross-service transactions should be avoided in favor of events and eventual consistency.

Prefer explicit contracts between services.

Design APIs, events, and ownership boundaries before designing deployment topology.

## Organize by Responsibility

Keep domain logic close to the feature it belongs to.
Place shared infrastructure in reusable modules.
Ensure features can depend on shared modules, but not the reverse.
Avoid scattering a feature's logic across unrelated directories.
Split oversized files or services when they accumulate multiple responsibilities.
Split by meaningful domain responsibility, not by tiny implementation details.
Prefer names that are clear without being noisy or over-explicit.
Use domain slices to avoid god files and services that mix unrelated workflows.

For feature modules in any backend stack, prefer a domain-first shape where each feature owns its entrypoint, composition root, API and architecture documentation when substantial, tests, persistence access, business services, and jobs. Adapt filenames to the language/framework, but preserve the responsibility boundaries:

```txt
src/<feature>/
  <feature>.<entrypoint>        # controller/router/handler/gateway
  <feature>.<composition-root>  # module/provider registration/bootstrap wiring
  <feature>.constants           # feature constants, keys, defaults, limits
  <feature>.types               # feature-owned domain/transport types when useful
  dto/                          # request/response validation and transport shapes
  API.md                        # domain-owned contract map
  ARCHITECTURE.md               # domain-owned structure, flow, and invariants
  docs/                         # generated documentation helpers or focused examples
  test/                         # focused tests for this feature
  repositories/                 # persistence access only
  services/                     # business logic and use cases
  jobs/                         # workers/processors/queue producers owned by feature
```

Every backend project owns these root documents:

```txt
README.md        # what the project is, how to run it, core capabilities
API.md           # public and internal contract map with links to authoritative specifications
ARCHITECTURE.md  # system shape, module boundaries, state, jobs, caching, realtime, recovery
```

In a multi-service repository, give every deployable backend service the same focused `README.md`, `API.md`, and `ARCHITECTURE.md`.
Give every substantial domain concise `API.md` and `ARCHITECTURE.md` files beside its code.
Root documents map the whole system; service and domain documents explain only the contracts and architecture they own.
Link to authoritative OpenAPI, protobuf, event, or schema definitions instead of duplicating them.

Keep these documents complete but do not update them merely because code changed.
Update `API.md` when its owned contract or observable behavior changes.
Update `ARCHITECTURE.md` when its owned structure, responsibilities, dependencies, major flow, or durable invariants change.
A fix that makes the implementation conform to the documented design normally needs no API or architecture edit.
If reference documentation was incomplete or incorrect, rewrite or consolidate the existing explanation instead of appending details that memorialize the fix.
Do not accumulate code-level steps, defensive checks, and isolated failure scenarios unless they are necessary to understand the enduring design.
Use direct, concise language and keep each fact at the document's abstraction level.

Use `docs/` inside feature modules for framework-generated documentation helpers or feature-specific examples.

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

## Schema Design

Prefer lean schemas where every table, column, index, and persisted field has a current functional purpose.
Do not add decorative, speculative, or future-maybe fields just because they could become useful later.
Before adding a column, name the behavior it supports now: validation, querying, authorization, billing, reporting, auditing, integration, recovery, or another concrete product need.
Do not use JSON as a shortcut for unclear schema design. Prefer explicit columns and relational tables when data has stable meaning or needs validation, querying, sorting, filtering, permissions, reporting, or business rules. Use JSON only when the payload is intentionally flexible, stored as a snapshot, or shaped by an external system and the application does not need to query or enforce rules on its inner fields.
If a field is only for possible future analytics, display convenience, or hypothetical audit history, leave it out until the feature actually needs it.

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
Use Open Telemetry as instrumentation layer.
Use the product's observability stack deliberately: APM/log platforms such as New Relic, or open stacks such as Prometheus, Grafana, Tempo, and Loki.
When selecting observability tooling, follow the stack already present in the repo or deployment environment instead of introducing a competing platform.
Emit useful logs, metrics, traces, and security/audit events without leaking sensitive data.
For distributed systems, design for retries, timeouts, circuit breakers, backpressure, graceful degradation, and eventual consistency.

## Observability

Observability exists to support correctness, reliability, debugging, recovery, and operations.

Do not introduce significant architectural complexity solely for observability requirements.

Observability should help answer:

- Is the system healthy?
- Is the system behaving correctly?
- What failed?
- Why did it fail?
- Can it be recovered safely?

Prioritize:

1. Domain correctness
2. Authorization
3. Data integrity
4. Failure handling
5. Recovery
6. Observability

Observability should support these concerns, not replace them.

Give every signal one clear responsibility:

- Metrics measure contextual system and business health and power alerting.
- Traces explain individual request, job, message, and dependency execution.
- Structured application logs record meaningful outcomes, degradation,
  failures, retries, recovery, and process lifecycle.
- Activity logs explain user-visible business activity.
- Audit logs establish durable security or compliance facts.
- Ingress access records, when operational or forensic requirements need
  them, provide minimal transport evidence outside the application.

Do not require the application to print a generic successful request-completed
log for every request when contextual metrics and tracing already own that
information. Disable or sample such automatic application access logs only
after metrics and traces cover the flow. Retain minimal ingress access records
separately when the threat model, incident-response policy, or regulatory
requirements require a complete transport record.

Do not use access logs as activity or audit truth. Do not duplicate the same
request outcome into ingress logs, application logs, traces, and metrics
without a defined investigative purpose and retention policy.

Instrument the four golden signals around stable, contextual operations rather
than relying only on generic process-wide or protocol-wide totals:

- latency;
- traffic;
- errors;
- saturation.

Use bounded operation names such as authentication login, order capture,
document extraction, queue publication, or reconciliation. Measure business
outcomes separately from infrastructure health. Use metrics for high-volume
success paths, workflow outcomes, queue depth and oldest age, retry rates,
dependency health, and resource saturation.

Prefer low-cardinality metric dimensions such as stable operation, route
template, status class, worker name, queue name, provider, event type, and
bounded error category. Never use raw URLs, query strings, user IDs, emails,
tenant IDs, document IDs, request payloads, or arbitrary identifiers as metric
labels.

Trace bounded units of work such as requests, background jobs, webhook
processing, integration calls, queue consumers, and significant realtime
messages. Preserve trace context across HTTP, jobs, queues, message brokers,
webhooks, and external providers so a trace can follow a business workflow
across boundaries. Do not trace connection lifetimes, heartbeats, or routine
keepalive traffic.

Prefer structured logs over interpolated strings. Include stable event and
operation names, outcomes, bounded error codes, and relevant correlation
identifiers such as request ID, trace ID, job ID, message ID, tenant ID, actor
ID, and domain entity ID when those identifiers are safe and necessary for
investigation. Do not put high-cardinality identifiers into metric labels.

Classify log severity by operational meaning, not merely protocol status.
Expected validation, authorization, not-found, rate-limit, and conflict
responses are not automatically warnings or errors. Log a handled condition at
warning level only when it represents degradation or requires operational
attention. Log unexpected failures and exhausted recovery at error level.

Never send raw URLs, query strings, secrets, tokens, cookies, credentials,
request bodies, documents, prompts, or sensitive payloads to logs or traces.
Use normalized route templates and explicit operation names. Redact before
telemetry leaves the process rather than relying only on downstream filters.

### Observability Setup Workflow

When asked to set up monitoring, dashboards, alerts, logging, tracing, or observability, first identify the active observability stack from project files, environment variables, deployment manifests, and user context. Do not assume New Relic, Prometheus/Grafana, ELK/OpenSearch, OpenTelemetry, or any specific vendor unless the repo or user confirms it.

Adapt the setup to the chosen stack:

- **New Relic**: use APM entities, logs, infrastructure samples, NRQL dashboards, alert policies, workflows, transaction tracing, error collection, and slow-query visibility.
- **Prometheus/Grafana stack**: use Prometheus metrics, Grafana dashboards, Alertmanager rules, Loki logs, Tempo traces, and OpenTelemetry instrumentation where applicable.
- **ELK/OpenSearch**: use structured application logs, index mappings, dashboards, saved searches, alert rules, and trace correlation if APM is present.
- **OpenTelemetry-first setups**: instrument services with OpenTelemetry APIs/SDKs and route metrics, traces, and logs to the configured backend.

Start with clean signal before dashboards or alerts:

1. Confirm service identity and stable environment labels.
2. Suppress noisy successful probes such as liveness/readiness checks while preserving readiness failures.
3. Ensure structured logs include stable query fields such as service name, role, instance id, request id, trace id, user id, job id, queue name, and domain entity ids.
4. Ensure expected client/application errors are not logged at error level.
5. Confirm logs, metrics, traces, and infrastructure data arrive under the intended service/entity names.

Create dashboards only after signal quality is acceptable. Prefer widgets that show throughput, latency percentiles, error rate, error and warning logs, operational error tables with correlation ids, CPU, memory, disk, dependency health, queue health when workers exist, and business workflow outcomes when available.

Create alerts after dashboards. Alerts must be actionable and low-noise. Prefer sustained error-rate increases, latency degradation, readiness/dependency failures, error-level application logs, resource saturation, queue backlog/retry exhaustion, failed recovery jobs, and critical business workflow failures. Avoid alerting on every transient failure, successful health probe, routine 4xx, normal reconnects, or metrics that do not require human action.

Set notification routing after alert conditions exist. For any stack, verify that alerts actually notify through the intended workflow, contact point, receiver, destination, or escalation path.

A technically healthy service can still be failing business workflows.

Design alerts around actionable failures.

Prefer alerts for:

- sustained error rate increases
- SLO violations
- dependency outages
- queue backlogs
- failed recovery workflows
- abnormal latency increases

Avoid alerts that do not require human action.

Avoid alerting on every transient failure.

Favor alerts that indicate user impact, operational risk, or recovery failure.

Keep secrets, tokens, passwords, cookies, authentication credentials, and sensitive payloads out of telemetry.

Redact or omit sensitive information before it reaches logs, traces, metrics, or audit systems.

Observability is a supporting concern.

A perfectly instrumented system with incorrect business behavior is still a broken system.


## Reliability Patterns

Assume failures are normal.

Distinguish between:
- transient failures
- permanent failures
- partial failures

Retry only when operations are safe to repeat.

Use:
- idempotency keys
- outbox pattern
- inbox pattern
- deduplication
- sagas
- compensating actions

Understand:
- at-most-once delivery
- at-least-once delivery
- exactly-once tradeoffs

Prefer eventual consistency when strong consistency is unnecessary.

Design recovery paths before implementing happy paths.

## Queues, Brokers, and Async Boundaries

Use queues for background work and message brokers for async domain events, pub/sub, integration events, and cross-service communication.
Choose queue or broker semantics deliberately: work distribution, pub/sub fanout, ordered streams, delayed jobs, retry workflows, or event sourcing are different needs.
Do not add a broker just because it is available; use it when async decoupling, fanout, buffering, integration boundaries, or independent consumers are valuable.
Assume at-least-once delivery unless the system proves otherwise.
Make consumers idempotent and safe to retry.
Use dead-letter or failed-job inspection for important workflows.
Keep events small, versioned, and tied to stable business meaning.
Prefer explicit domain events over leaking database row shapes as event payloads.

## Event Design

Events represent completed business facts.

Prefer:
- OrderCreated
- PaymentCaptured
- UserRegistered

Avoid:
- SaveOrder
- UpdateUser

Keep events:
- immutable
- versioned
- business-oriented

Do not expose internal database schemas through event payloads.

Treat events as public contracts once consumed by other systems.

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

## Data Lifecycle

Model the lifecycle of data explicitly.

Design for:
- retention
- archival
- expiration
- deletion
- recovery

Not all data should live forever.

Understand legal, operational, and storage implications of long-lived data.

Separate operational, historical, and analytical data when requirements differ.

Define ownership and cleanup responsibilities for data created by features, jobs, integrations, and uploads.

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

## Recovery Engineering

Design systems for recovery.

Understand:
- replay
- reprocessing
- backfills
- reconciliation jobs
- disaster recovery

Keep important workflows replayable.

Prefer append-only audit trails and durable execution records when recovery or investigation is required.

Design recovery procedures before incidents occur.

A system that cannot recover safely is not fully reliable.

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
