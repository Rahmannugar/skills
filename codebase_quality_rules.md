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

## Documentation Integrity

Treat reference documentation as the canonical description of the system, not as evidence that implementation work occurred.
Preserve the project's required root, service, and domain documentation structure and keep each document at its intended scope.
Update a document only when the contract or durable system truth owned by that document changed or was materially incorrect.
A bug fix that restores already-documented behavior does not require an API or architecture documentation change.

Keep API documentation focused on observable contracts and behavior.
Keep architecture documentation focused on durable structure, ownership, dependencies, major flows, and important invariants.
Do not append implementation details, defensive checks, or individual failure scenarios merely because they mattered to the latest fix.
When a documentation change is necessary, rewrite or consolidate the canonical explanation instead of adding a repair-specific sentence or paragraph.
Ask whether the detail would still deserve space if the system had been implemented correctly from the beginning; omit it if not.

Use plain language, short sentences, and the minimum detail needed at the document's abstraction level.
Keep implementation progress and repair history in progress, handoff, or changelog documents rather than API or architecture references.

## Testing Expectations

Treat tests as proportionate evidence for a change, not as a separate delivery goal. Follow the repository's established test strategy and scale effort with behavioral risk, complexity, and blast radius.
Before writing or expanding tests, identify the realistic regression, invariant, security boundary, failure mode, integration contract, or meaningful side effect they protect. Do not chase arbitrary coverage, exhaustive inputs, large test counts, static types, trivial assignments, or behavior owned by the language, framework, or dependency.

Test observable behavior at the boundary that owns the risk. Specialized engineering skills may add boundary-specific guidance, but must not replace or repeat this general policy.

Do not write mock-return echoes, duplicate the implementation sequence, or reproduce production logic inside a fake oracle. Mocks may establish relevant conditions and verify business-significant outgoing effects; an interaction is valid when the command itself is the contract.

Keep each test focused on one coherent behavior. Use table-driven cases for several inputs exercising the same rule. Split tests that hide independent failures, but do not cosmetically split tests while production responsibilities remain oversized.

For changed behavior, improve the relevant existing tests before creating new test files or suites. Add a new test location only when it owns a genuinely distinct responsibility or boundary.
Run the smallest relevant validation first, then broaden it according to cross-cutting risk and repository conventions. Do not interrupt normal delivery for a general test rewrite or introduce a new test framework without a reproducible defect, unsafe blind spot, unreliable test infrastructure, or explicit request.
A regression test must exercise the faulty behavior and should fail before the fix when practical. Never weaken a test to hide flawed production behavior.

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

## Command, Commit, And Closeout Rules

Do not auto-commit unless the user explicitly asks.

When operating in a guidance-only mode, do not run CLI commands. Specify the commands that should be executed instead.

When closing out a completed implementation slice, provide a conventional commit message unless the user explicitly says not to.

Use a commit type such as `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, or `build`.

Keep the subject line concise and specific. Use the body only when extra context matters.

Before suggesting a commit message, summarize validation that passed or clearly state what was not run.

When giving commit commands, use `git add .`.
