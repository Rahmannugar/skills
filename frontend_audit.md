## frontend_logic_audit

Perform a comprehensive technical audit of the frontend application with a focus on functional correctness, UI state behavior, and reliability of client-side logic.

The goal is to uncover hidden bugs, incorrect state transitions, broken user flows, and edge cases that could cause the interface to behave incorrectly.

This audit focuses strictly on behavior and correctness, not styling or formatting.

---

### Scope of the Audit

Examine the frontend system holistically, including:

- component logic
- UI state transitions
- asynchronous data flows
- validation behavior
- user interaction flows
- error handling scenarios
- accessibility behavior
- client-side security implications
- inconsistent behavior between components
- incorrect assumptions in UI logic
- caching and data synchronization behavior

Focus on how the interface behaves across different states and interactions.

---

### Areas of Investigation

Evaluate the correctness of system behavior across the following areas.

---

### UI State Transitions

Check whether UI state transitions behave correctly during:

- loading data
- handling errors
- submitting forms
- receiving empty responses
- updating cached data
- conditional rendering flows

Identify situations where the UI could enter inconsistent or invalid states.

---

### Async Behavior

Analyze asynchronous flows such as:

- API requests
- mutations
- optimistic updates
- retries and refetching
- parallel requests

Look for:

- race conditions
- stale data problems
- broken loading states
- incorrect sequencing of async operations.

---

### State Management

Audit state handling across:

- local component state
- global state
- server state

Look for:

- duplicated state
- stale closures
- conflicting state updates
- incorrect derived state
- state that can fall out of sync with the source of truth.

---

### Data Fetching and Caching

Examine how the system fetches and synchronizes data.

Look for:

- stale cache usage
- incorrect query invalidation
- inconsistent query keys
- UI rendering outdated data
- assumptions that cached data is always fresh.

---

### User Interaction Flows

Analyze how user actions propagate through the system.

Examples include:

- form submission flows
- navigation logic
- multi-step interactions
- conditional UI flows
- modal workflows
- dynamic UI state changes.

Identify situations where user actions could cause inconsistent UI states.

---

### Validation Logic

Ensure client-side validation behaves consistently.

Look for:

- missing validation paths
- inconsistent validation across components
- validation that allows invalid states to propagate
- incorrect validation order.

---

### Accessibility Behavior

Audit functional accessibility issues such as:

- broken keyboard interactions
- incorrect focus management
- inaccessible interactive elements
- navigation traps
- inaccessible error messages.

---

### System Architecture and Design Risks

Evaluate architectural patterns that may introduce long-term instability.

Look for:

- tight coupling between components
- duplicated logic across components or hooks
- components relying on unstable backend assumptions
- inconsistent state ownership
- fragile UI flows dependent on timing of async operations.

Identify design patterns that may cause reliability issues as the application grows.

---

### Files That Are Too Large to Maintain

Identify files that have grown excessively large or complex.

Large files often indicate:

- hidden coupling between concerns
- multiple responsibilities within one component
- logic that should be extracted into hooks or utilities.

Flag files that are difficult to reason about or maintain due to their size or complexity.

---

### Important Rules

- Do not rewrite or refactor code automatically.
- Do not focus on styling, formatting, or naming conventions.
- Do not propose UI redesigns.
- Focus only on logic correctness and behavioral reliability.

---

### Output Format

For every issue discovered, provide:

- Issue Title
- Location in Code (component, hook, or file)
- Description of the Problem
- Why It Is a Problem
- Possible Failure Scenario or Edge Case
- Severity Level (Critical, High, Medium, Low)

---

### Systemic Issues

If patterns of problems appear across multiple areas of the system, summarize them under Systemic Issues.

Examples include:

- repeated state management flaws
- inconsistent async patterns
- duplicated validation logic
- fragile UI state patterns.

---

### Goal of the Audit

Expose hidden UI logic failures, incorrect state transitions, and edge cases before any fixes are attempted.

The goal is to understand where the interface can break under real user conditions.
