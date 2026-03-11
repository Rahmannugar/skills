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

### File and Module Size

Avoid extremely large files or modules that accumulate too many responsibilities.

Large files often indicate:

- hidden coupling between features
- mixed domain concerns
- difficult-to-maintain logic
- unclear ownership of behavior

Prefer splitting logic into smaller modules when files become complex or difficult to reason about.

Backend systems should prioritize clear domain boundaries and manageable module sizes.

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

Services should remain focused on domain behavior.

Avoid turning services into large orchestration layers that manage unrelated responsibilities.

---

### Service Size and Responsibility

Services must remain focused and manageable in size.

A service should:

- represent a clear domain capability
- coordinate a limited set of related operations
- remain easy to reason about and test

If a service grows too large or handles multiple workflows, extract additional domain services.

Large service files often hide:

- duplicated domain rules
- complex control flows
- tightly coupled responsibilities.

---

### Domain Invariants and Business Rules

Critical domain rules must be enforced consistently across the system.

Examples include:

- account balances must never become negative
- resources cannot be modified after deletion
- users cannot access resources outside their authorization scope

These invariants must always be enforced inside domain services.

Never rely solely on:

- controllers
- frontend validation
- client assumptions

Backend logic must enforce the system's source-of-truth rules.

Violating domain invariants is a common source of production incidents.

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

### Database Indexing

Database schemas should include appropriate indexes to support common query patterns.

Indexes should be created for:

- frequently filtered fields
- foreign key relationships
- fields used for sorting
- fields used for pagination
- fields used in joins

Queries that scan large tables without indexes should be avoided.

Avoid excessive indexing that negatively impacts write performance.

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

Startup failures must terminate the process.

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

---

### Health Checks

Backend systems should expose health endpoints.

Liveness checks confirm the process is running.

Readiness checks confirm the application is ready to serve traffic.

Dependencies to verify include:

- database connectivity
- cache availability
- external services

---

### API Response Consistency

API responses should follow a consistent structure across endpoints.

Success responses should return predictable data shapes.

Error responses should follow a standardized format with meaningful error messages.

---

### Pagination

Endpoints returning large datasets should implement pagination.

Avoid returning unbounded collections.

Support pagination parameters such as:

- page
- cursor
- limit

Pagination responses should include metadata for safe navigation.

---

### Request Context

Each request should include a unique request identifier.

Request identifiers allow:

- request tracing
- log correlation
- easier debugging

Logs should include request context.

---

### Logging and Observability

Logging must be structured and contextual.

Logs should capture:

- request identifiers
- service context
- state transitions
- error details

Avoid excessive or noisy logging.

---

### Error Handling

Error handling must be centralized.

Errors should be categorized such as:

- validation errors
- domain errors
- infrastructure errors

Internal implementation details must never be exposed to clients.

Errors must be logged with context.

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

Operations that must succeed together should use transactions.

Avoid global mutable state.

---

### Configuration Management

Configuration must be centralized.

Configuration includes:

- environment variables
- database settings
- cache settings
- external service configuration

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

Comments should only explain:

- complex algorithms
- non-obvious architectural decisions
- complex infrastructure logic.

---

### Scalability and Maintainability

Every implementation must consider long-term maintainability.

The codebase should remain:

- modular
- predictable
- easy to extend
- safe to refactor

Avoid shortcuts that introduce technical debt.
