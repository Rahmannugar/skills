---
name: codebase-quality-rules
description: Quality and consistency rules for editing an existing codebase. Use when an assistant should preserve established patterns, keep changes narrowly scoped, maintain strong typing, avoid placeholder logic, add meaningful tests when appropriate, and produce implementation guidance without drifting into unrelated refactors.
---

# Codebase Quality Rules

## Preserve Consistency

Match the existing coding style, naming conventions, file structure, and architectural patterns.
Prefer extending established patterns over inventing new ones.
Avoid introducing new abstractions, folder structures, or architectural styles when the project already has a clear approach.
When a project has feature modules, keep feature-owned services, repositories, docs, tests, and jobs inside that feature.
Keep shared reusable infrastructure under the established infra/shared location.

## Keep Scope Tight

Stay within the requested task.
Avoid modifying unrelated files, modules, or architectural boundaries unless the work clearly requires it.
Do not introduce broad refactors without strong justification.
Respect the requested collaboration mode.
When the user asks to review, discuss, explain, give snippets, or give a slice, provide guidance without editing files.
Only make edits when the user explicitly asks to auto, autodo, implement, fix, or otherwise perform the change.

## Maintain Engineering Standards

Favor scalable and maintainable solutions.
Apply DRY and SOLID where they materially improve the implementation.
Keep module boundaries clear.
Use explicit, correct types and never use `any`.
Implement real logic, not placeholders or stubs.
Prefer simple, readable solutions over unnecessary indirection.
Avoid "do the simple thing now and improve later" when the missing piece is correctness, security, observability, idempotency, or cleanup.
Factor external clients behind clear abstractions when the integration should be replaceable.
Do not keep facade files that add no value after a domain service split.
Do not leave duplicate old files after moving or splitting modules.
Update imports, module wiring, tests, docs, generated references, and path aliases after moving files.
After structural changes, run or recommend validation that checks build, tests, lint, and route/module startup where appropriate.
Avoid `any`; if an integration boundary requires a narrow cast, keep it local and explain why the runtime value is safe enough.

## Naming and Comments

Use clear, descriptive names.
Avoid ambiguous or overly abbreviated identifiers.
Avoid names that are needlessly verbose or encode implementation details that do not help the caller.
Keep comments minimal and intentional.
Write comments only when they clarify non-obvious logic.

## Testing Expectations

Write tests where they provide confidence, not merely coverage.
Scale the number and depth of tests with behavioral complexity, risk, and blast radius; do not impose an arbitrary test count.

Use unit tests for business decisions, invariants, validation, state transitions, error mapping, and meaningful side effects that become clearer with dependencies isolated.
Include a successful path when it proves meaningful behavior, then cover distinct rejection, boundary, and failure paths.
Each test should protect a separate behavior or risk.

Do not write tests that only configure a mock to return a value and assert that the service returns the same value.
Do not duplicate the implementation sequence through mock expectations.
Do not test framework or third-party library behavior as if it were application logic.
Use mocks to establish relevant conditions and verify only business-significant interactions.

Use table-driven tests when several inputs exercise the same rule and should produce the same class of outcome.
Do not create many nearly identical test cases when one clearly named table-driven test communicates the behavior better.

Use integration tests when confidence depends on real boundaries, including:
- database constraints, transactions, and repository mapping
- Redis expiry, invalidation, and atomic consumption
- concurrency, locking, races, and idempotency
- provider adapters and external-client contracts
- HTTP cookies, guards, middleware, and request/response wiring

Use end-to-end tests sparingly for critical journeys that must prove several boundaries work together.
Do not simulate persistence or concurrency guarantees with unit-test mocks.
Avoid testing observability unless telemetry is itself part of the functional contract.

Test observable outcomes rather than private methods or incidental implementation details.
Add a regression test when fixing a real bug if it would have failed before the fix.
Do not weaken tests to hide flawed behavior; fix the implementation.
Prefer a focused suite of high-information tests over a large suite of repetitive assertions.

## Versioning

Use semantic versioning for backend services, SDKs, packages, and public API contracts.

The format is:

`MAJOR.MINOR.PATCH`

Use `1.0.0` for the first stable production release.

Increase PATCH when the release only fixes existing behavior and should not require users or clients to change anything.

Example: `1.0.0` -> `1.0.1`

Increase MINOR when the release adds new functionality without breaking existing users or clients.

Example: `1.0.1` -> `1.1.0`

Increase MAJOR when the release breaks existing users or clients and requires them to change code, requests, responses, configuration, or behavior.

Example: `1.1.0` -> `2.0.0`

When unsure, ask: "Can an existing client upgrade without changing anything?"

If yes, use PATCH or MINOR.

If no, use MAJOR.

## Command and Commit Rules

Do not run CLI commands while operating strictly under this skill.
Specify the commands that should be executed instead.
When closing out a feature, provide a commit message.
Use a commit type such as `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, or `build`.
Keep the subject line to 50 characters or fewer.
Place extra context in the commit body.
When giving commit commands, use `git add .`.
