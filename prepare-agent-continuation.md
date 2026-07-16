---
name: prepare-agent-continuation
description: Prepare high-density continuation context for another coding agent or a future agent session. Use when work must be resumed with exact objectives, decisions, repository state, changed files, validation results, risks, blockers, and next actions preserved without redoing completed work.
---

# Prepare Agent Continuation

Create an evidence-based operational brief that lets another agent continue immediately and safely.

## Gather current state

- Read the latest user objective and treat superseded requests as historical context.
- Inspect goal or plan state when present.
- Inspect repository status, relevant diffs, changed files, and untracked files.
- Record validation from actual command results. Do not infer that a test, lint, build, migration, or runtime check passed.
- Separate the agent's changes from pre-existing or user-owned work when known.
- Capture decisions, naming, business rules, exclusions, and explicit no-fallback requirements from the conversation.

Do not modify the workspace merely to prepare the brief.

## Use this continuation structure

Include sections only when they contain useful information:

```txt
Current objective

Agreed decisions and invariants

Completed work

Repository and changed-file state

Validation performed

Known issues, warnings, and unverified assumptions

Remaining work in dependency order

Immediate next action

Operational details needed to continue
```

For multi-repository work, group completed work, changed files, validation, and remaining work by repository.

Name exact files and commands when they matter. Include relevant routes, schemas, migrations, query keys, environment constraints, service boundaries, or test names that the next agent would otherwise have to rediscover.

## Preserve decision quality

- Distinguish completed, implemented-but-unverified, pending, and blocked work.
- Call something blocked only when progress requires unavailable input or authority.
- Record why a non-obvious decision was made, but do not replay the whole discussion.
- State what must not be reintroduced, such as aliases, fallbacks, offset pagination, duplicate files, or stale contracts.
- Identify temporary compatibility code, follow-ups, or deferred refactors explicitly.
- Preserve safe sequencing when one repository or migration must land before another.

## Keep the brief operational

- Use dense bullets and short factual paragraphs.
- Avoid tutorials, product prose, praise, and chronological narration.
- Do not write a colleague-facing API handoff; use `write-developer-handoff` for that audience.
- Do not include secrets, tokens, credentials, or unnecessary personal data.
- Do not claim a clean worktree, successful deployment, or passing validation without evidence.
- Mention failed or unavailable validation and its exact reason.
- Do not tell the next agent to redo work already completed.

## Check the continuation before delivery

Confirm that the next agent can answer:

- What outcome is currently required?
- What decisions are fixed?
- What has actually been changed?
- What validation passed, failed, or was not run?
- What existing user work must be preserved?
- What remains, in what order, and what is the next concrete action?
