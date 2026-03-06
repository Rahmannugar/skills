## backend_engineering

Build backend systems that are scalable, reliable, and maintainable.

Design systems that can evolve safely as the application grows.
Respect existing architecture and patterns in the codebase.
Do not introduce conflicting patterns unless there is a strong architectural reason.

---

### Architecture Philosophy

Prefer feature-based architecture.

Each feature should encapsulate its own domain logic including:

- controllers or handlers
- services
- repositories
- validators
- types
- routes
- tests

Avoid scattering domain logic across unrelated directories.

Shared infrastructure should exist separately and be reusable across features.

---

### Project Structure

Structure the codebase around features and shared infrastructure.

Typical structure:

```
src
  features
  shared
  clients
  jobs
  cache
  middleware
  config
  db
  utils
  types
  tests
```

Feature modules contain domain logic.
Shared modules contain infrastructure and reusable tools.

Features should depend on shared modules, not the other way around.

---

### Controllers / Handlers

Controllers must remain thin.

Responsibilities:

- parse requests
- validate inputs
- call services
- return responses

Controllers must not contain business logic.

They are responsible only for the request/response layer.

---

### Services

Services contain business logic and domain workflows.

Responsibilities:

- enforce domain rules
- coordinate repositories
- call external clients
- orchestrate domain operations

Services must remain independent from HTTP frameworks and controllers.

---

### Repositories

Repositories encapsulate all persistence logic.

Responsibilities:

- execute queries
- persist domain data
- map database records

Repositories should be the only layer interacting directly with the database.

Use ORMs or structured data mappers for database interaction.

Database queries must not appear in controllers or services.

Transactions should be used for operations that must succeed or fail atomically.

---

### Validation

All external input must be validated before business logic executes.

Validation should occur at request boundaries.

Validation rules should be explicit and centralized.

Never trust external input.

---

### External Clients

External integrations must live in a dedicated clients layer.

Clients handle communication with external services such as:

- payment providers
- email services
- file storage
- analytics platforms

Clients should manage:

- request logic
- error handling
- retries when appropriate

Services interact with clients rather than calling external APIs directly.

---

### Background Jobs

Background jobs should be used for long running or asynchronous tasks.

Examples include:

- email delivery
- report generation
- file processing
- scheduled workflows
- heavy computations

Jobs must be:

- idempotent
- retryable
- observable

Heavy work must not block the request-response cycle.

Jobs must not rely on request context.

---

### Caching

Caching should be introduced when it improves performance.

Typical caching targets:

- expensive database queries
- computed results
- external API responses
- frequently accessed resources

Caching must include a clear invalidation strategy.

Avoid stale or inconsistent data.

Caching should exist as a dedicated infrastructure layer.

---

### Application Lifecycle

Backend services must manage startup and shutdown correctly.

Startup should include:

- configuration loading
- database connection initialization
- external service initialization
- job worker initialization when applicable

Startup failures must terminate the process rather than leaving the system in an inconsistent state.

Infrastructure connections may include retry strategies with exponential backoff.

Retries must always be bounded.

---

### Graceful Shutdown

Applications must support graceful shutdown.

Shutdown procedures must:

- stop accepting new requests
- allow in-flight requests to complete
- close database connections
- stop background workers
- release infrastructure resources

Graceful shutdown prevents request loss and data corruption.

---

### Health Checks

Backend systems should expose health endpoints.

Liveness checks confirm the process is running.

Readiness checks confirm the application is ready to serve traffic.

Readiness checks should verify critical dependencies such as:

- database connectivity
- cache availability
- required external services

If dependencies fail, readiness checks must fail.

---

### Request Context

Each request should include a unique request identifier.

Request identifiers allow:

- request tracing
- log correlation
- easier debugging

Logs generated during request processing should include the request context.

---

### Logging and Observability

Logging must be structured and contextual.

Logs should capture:

- request identifiers
- service context
- important state transitions
- error details

Logs should help diagnose issues in production.

Avoid excessive or noisy logging.

---

### Error Handling

Error handling must be centralized.

Errors should be categorized such as:

- validation errors
- domain errors
- infrastructure errors

Responses should remain consistent across the API.

Internal implementation details must never be exposed to clients.

Errors must always be logged with sufficient context.

---

### Middleware

Middleware should handle cross-cutting concerns such as:

- authentication
- authorization
- request logging
- rate limiting
- request parsing
- error handling

Middleware must not contain business logic.

---

### Concurrency and Data Integrity

Systems must behave correctly under concurrency.

Consider:

- race conditions
- transactional integrity
- idempotency
- concurrent job execution

Operations that must succeed together should be executed within transactions.

Avoid global mutable state.

---

### Configuration Management

Configuration must be centralized.

Configuration should include:

- environment variables
- database settings
- cache settings
- external service configuration

Configuration must not be scattered across the codebase.

Secrets must never be committed to source control.

---

### Shared Infrastructure

Shared infrastructure modules should exist for common functionality such as:

- logging
- database clients
- request id utilities
- error handling
- caching utilities
- configuration

Shared infrastructure should remain framework-independent when possible.

---

### Testing Strategy

Backend systems must include multiple layers of testing.

Unit tests validate domain logic and services.

Integration tests validate database interactions and API behavior.

Tests should validate real behavior rather than implementation details.

Avoid brittle tests.

---

### Security

Security must be treated as a core requirement.

Include:

- strict input validation
- authentication enforcement
- authorization checks
- protection against injection attacks
- safe handling of secrets

Sensitive information must never appear in logs.

---

### Code Quality

Follow modern language conventions and best practices.

Use:

- linters
- consistent formatting
- clear naming conventions

Prefer clarity over cleverness.

---

### Comments

Comments should remain minimal.

Code should be written clearly enough that behavior is understandable without explanation.

Comments should only explain complex algorithms, non-obvious architectural decisions, or complex infrastructure logic.

---

### Scalability and Maintainability

Every implementation must consider long-term maintainability.

The codebase should remain:

- modular
- predictable
- easy to extend
- safe to refactor

Avoid shortcuts that introduce technical debt.

Favor clear domain boundaries and maintainable patterns.
