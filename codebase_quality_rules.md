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

Add unit or integration tests when appropriate for the change.
Test real behavior and correctness.
Do not change tests to mask flawed logic.
If behavior is wrong, fix the implementation rather than weakening the test.
Avoid testing observability itself unless the feature's functional contract depends on it.
Mock dependencies in unit tests without turning the test into a duplicate of implementation wiring.
Prefer targeted tests for behavior that would be costly to regress.

## Command and Commit Rules

Do not run CLI commands while operating strictly under this skill.
Specify the commands that should be executed instead.
When closing out a feature, provide a commit message.
Use a commit type such as `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, or `build`.
Keep the subject line to 50 characters or fewer.
Place extra context in the commit body.
When giving commit commands, use `git add .`.
