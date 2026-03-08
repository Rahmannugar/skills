## codebase_quality_rules

Maintain high engineering standards and consistency with the existing codebase.

---

### Codebase Consistency

- Preserve existing coding style and conventions.
- Maintain consistency in naming, structure, and patterns.

---

### Scope Discipline

- Changes must remain within the scope of the requested task.
- Avoid modifying unrelated files, modules, or architectural patterns unless explicitly required.
- Do not introduce refactors that affect large portions of the codebase without clear justification.

---

### Pattern Consistency

- Follow the architectural and structural patterns already present in the codebase.
- Do not introduce new abstractions, folder structures, or architectural styles if the project already follows an established pattern.
- Prefer extending existing patterns over creating new ones.

---

### Architecture Principles

- Favor scalable and maintainable solutions.
- Follow DRY and SOLID principles where appropriate.
- Prefer clear module boundaries and separation of concerns.

---

### Implementation Standards

- Avoid placeholder or stub implementations.
- Implement real, working logic.
- Keep logic simple, readable, and maintainable.

---

### Simplicity

- Prefer simple and direct solutions.
- Avoid unnecessary abstractions, excessive indirection, or premature optimization.
- Introduce abstractions only when they solve a real and recurring problem.

---

### Naming

- Use clear, descriptive, and self-explanatory naming conventions.
- Avoid ambiguous or overly abbreviated identifiers.

---

### Comments

- Keep comments minimal and intentional.
- Only include comments when they add real value or clarify complex logic.

---

### Testing

- Where appropriate, add unit or integration tests.
- Tests must validate real behavior and correctness.
- Do not alter tests to pass flawed logic.
- If tests fail, fix the implementation rather than modifying the tests.

---

### CLI Commands

- Do not run CLI commands.
- Only specify which commands should be executed.

---

### Git Commit

- At the end of each feature, include a commit message.

Commit rules:

- Subject line must be no longer than 50 characters.
- Place additional context in the commit body.
