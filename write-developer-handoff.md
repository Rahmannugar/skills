---
name: write-developer-handoff
description: Write portable implementation handoffs for human software developers. Use when Codex must explain completed or canonical API, backend, frontend, mobile, data-contract, filtering, pagination, analytics, or UI-flow changes to a colleague who does not share the current workspace or prior conversation.
---

# Write Developer Handoff

Produce a practical reference a developer can implement from without access to the current workspace or conversation.

## Establish the source of truth

- Identify the recipient, the systems they own, and only the changes that affect them.
- Inspect authoritative contracts, API documentation, schemas, and agreed product behaviour before writing.
- Resolve field names, allowed values, count meanings, fallbacks, and edge cases from evidence. Do not guess.
- Keep internal implementation details out unless the recipient needs them to integrate correctly.

## Write each change as a usable contract

For each feature, include only the relevant parts of this pattern:

```txt
Feature or change

What changed

API or reference link

Expected request and response behaviour

Realistic example request, response, or user flow

UI rules, business meaning, and edge cases
```

Place a direct API documentation link beside the change it supports. Prefer a specific operation panel over a documentation homepage.

When a change removes compatibility, state the canonical field or behaviour and the absence of a fallback once, where it matters.

Use examples for cursor flows, filters, conditional fields, count semantics, renamed values, and other contracts that are easy to misread.

Reference an existing application for UI parity only when it is an intentional source of truth and the recipient can access it.

## Write for a human colleague

- Use normal headings, short paragraphs, bullets, and code blocks.
- Default to plain text mappings instead of Markdown tables. Use a table only when the user asks or it materially improves a dense comparison.
- Lead with the change, not an objective, plan, history, or rationale essay.
- Explain the minimum reason needed to understand a non-obvious business rule.
- Use direct, concrete language and the recipient's product vocabulary.
- Keep the document self-contained without becoming a beginner tutorial.

Do not include by default:

- local repository paths or changed-file inventories;
- agent status, tool activity, commits, tests, or dirty-worktree details;
- invented frontend types or architecture prescriptions;
- internal facts the recipient already knows, such as their own deployment status;
- objectives, acceptance ceremonies, long verification scripts, or a chronological changelog;
- prose that merely repeats the API example.

Include repository files, code types, or detailed implementation steps only when the user explicitly asks for them or the recipient owns that exact codebase and needs those details.

## Check the handoff before delivery

Confirm that:

- it remains useful if copied outside the current repository;
- a recipient with no backend or conversation context can understand every changed contract;
- important APIs have nearby direct links and realistic examples;
- filter visibility, count meanings, conditional fields, and removed fallbacks are explicit;
- it contains no workspace-specific or obvious internal commentary;
- it reads as a reference, not a tutorial, project plan, agent summary, or release story.
