---
name: write-developer-handoff
description: Write clear, portable implementation notes from the user to a developer who does not share the current workspace or conversation.
---

# Write Developer Handoff

Write the handoff as a personal note from the user to their colleague.

The recipient should understand:

- what I changed;
- what already works;
- what I need them to implement;
- the exact values, URLs, fields, or behaviour they need;
- what should happen when they finish.

They should not need access to my repository, previous messages, or knowledge of
how my side was implemented.

## Writing voice

Write in the first person, as though I am sending the note myself:

- “I have updated…”
- “The link now uses…”
- “I need you to…”
- “When the app receives this link…”

Address the recipient directly with “you” where appropriate.

Use natural colleague-to-colleague language. The result should sound like a
practical message I could copy and send, not generated documentation, an
engineering report, or an agent summary.

Do not mention Codex, tools, commits, tests, local file paths, investigation
history, or how the handoff was prepared.

## Content

Start with the change or result. Give only enough background to explain what
the recipient needs to do.

Include:

- the exact public URLs or API endpoints they need;
- realistic values or payload examples when these prevent misunderstanding;
- the behaviour their application must implement;
- important fallbacks and platform differences;
- identifiers or configuration values they must copy exactly.

Explain unfamiliar project-specific terms the first time they appear. Do not
explain normal concepts the recipient already works with.

Separate clearly:

1. what I have already done;
2. what I need the recipient to do;
3. the final expected flow.

Do not prescribe their internal architecture unless a specific implementation
detail is required for the integration to work.

## Keep it simple

Prefer short paragraphs and short numbered steps.

Do not turn a small change into a full specification. Remove:

- obvious statements;
- repeated behaviour;
- unnecessary warnings;
- long testing checklists;
- repository inventories;
- internal backend or frontend implementation details;
- history and rationale that do not affect implementation;
- generic instructions such as “handle errors appropriately.”

Mention a constraint once, where it becomes relevant.

Use headings only when they make the note easier to scan. Do not force every
handoff into the same template.

## Verify before writing

Inspect the source of truth before stating URLs, field names, identifiers,
allowed values, fallbacks, or platform behaviour. Do not guess.

Before delivering, confirm that:

- the note sounds like it came from me;
- someone outside the current conversation can understand it;
- the recipient knows exactly what they must implement;
- every copied value is correct;
- nothing irrelevant has been included.
