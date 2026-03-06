## codebase_quality_rules

Maintain high engineering standards and consistency with the existing codebase.

---

### Codebase Consistency

- Preserve existing coding style and conventions.
- Maintain consistency in naming, structure, and patterns.

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
