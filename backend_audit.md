## backend_logic_audit

Perform a comprehensive technical audit of the backend system with a focus on business logic correctness, data integrity, and system reliability.

The goal is to uncover hidden bugs, logical flaws, failure scenarios, and unsafe assumptions across the codebase.

The audit focuses strictly on functional correctness and system behavior, not style or formatting.

---

### Scope of the Audit

Examine the backend system across all modules, including:

- business logic implementation
- request processing flows
- validation logic
- data flow between services
- database interactions
- state transitions in domain logic
- concurrency behavior
- background job processing
- caching systems
- security implications caused by logic flaws
- inconsistent behavior across modules.

Focus on how the system behaves under both normal and abnormal conditions.

---

### Areas of Investigation

Evaluate correctness across the following areas.

---

### Business Logic

Audit whether domain logic behaves correctly under all conditions.

Look for:

- incorrect assumptions
- missing edge case handling
- inconsistent domain rules
- incorrect branching logic.

---

### Data Integrity

Analyze how data moves through the system.

Look for:

- partial writes
- inconsistent state transitions
- missing transaction boundaries
- operations leaving the system in invalid states.

---

### Database Interaction

Evaluate database access patterns.

Look for:

- missing transactions
- unsafe concurrent updates
- assumptions about database consistency
- queries that can produce inconsistent data.

---

### Concurrency and Race Conditions

Analyze situations where concurrent requests could cause failures.

Examples include:

- duplicate submissions
- conflicting updates
- job execution overlaps
- race conditions between services.

---

### Background Jobs and Queues

Audit asynchronous processing.

Look for:

- non-idempotent jobs
- duplicate job execution
- jobs depending on request state
- failure scenarios leaving jobs partially executed.

---

### Caching Behavior

Examine how caches interact with the source of truth.

Look for:

- stale cache usage
- cache invalidation problems
- inconsistent data between cache and database.

---

### Validation Logic

Ensure input validation is complete and consistent.

Look for:

- missing validation paths
- inconsistent validation between endpoints
- validation bypass scenarios.

---

### Security Implications

Identify logic flaws that could cause security vulnerabilities such as:

- broken authorization logic
- unsafe assumptions about authenticated users
- privilege escalation through incorrect logic paths.

---

### System Architecture and Design Risks

Evaluate architectural patterns that may introduce hidden reliability problems.

Look for:

- hidden coupling between services
- duplicated domain logic
- unclear ownership of business rules
- fragile async workflows
- modules bypassing domain boundaries.

Identify structural weaknesses that could cause systemic failures.

---

### Files That Are Too Large to Maintain

Identify files or modules that have become excessively large.

Large files often hide:

- multiple responsibilities
- hidden business logic
- complex control flows
- fragile dependencies.

Flag files that are difficult to reason about or maintain due to their size or complexity.

---

### Important Rules

- Do not automatically fix or rewrite the code.
- Do not focus on style, formatting, or naming conventions.
- Do not propose refactors unless they directly address a logic flaw.

The goal is to expose problems, not implement solutions.

---

### Output Format

For every issue discovered, provide:

- Issue Title
- Location in Code (file, module, or function)
- Description of the Problem
- Why It Is a Problem
- Possible Failure Scenario or Edge Case
- Severity Level (Critical, High, Medium, Low)

---

### Systemic Issues

If patterns of problems appear across multiple modules, summarize them under Systemic Issues.

Examples include:

- repeated transaction misuse
- duplicated domain logic
- unsafe async workflows
- inconsistent validation rules.

---

### Goal of the Audit

Expose hidden logic vulnerabilities, reliability risks, and failure points before any fixes are attempted.

The outcome should provide a clear understanding of where the backend can break under real-world conditions.
