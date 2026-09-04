---
name: frontend-engineering
description: Frontend architecture and implementation guidance for scalable, production-ready user interfaces. Use when an assistant is building, extending, or reviewing frontend features and should preserve project patterns, keep UI logic modular, use strong typing, handle async state safely, and maintain accessible, maintainable client-side behavior across web, mobile, and cross-platform clients.
---

# Frontend Engineering

## Architectural Posture

Build frontend systems that are scalable, maintainable, and production ready.
Respect the existing project architecture and visual system.
Favor clear separation of concerns across pages, components, hooks, services, API code, stores, types, and utilities.
Avoid dumping business or data-fetching logic into components.
Split large files when they mix multiple responsibilities.
Apply the same principle across React, Vue, Svelte, mobile clients, server-rendered apps, and native UI stacks: keep UI rendering, state ownership, data access, validation, and domain workflows distinct.
Prefer domain-first organization over generalized buckets when the product has clear domains.

## Data and State

Use a structured data layer instead of fetching directly in components.
Prefer an HTTP client, service layer, and hooks.
Use the project's established HTTP client.
Use the project's established server-state library for caching, refetching, deduplication, and loading or error handling.
Keep access tokens in memory when the backend uses refresh-token cookies.
Let auth refresh flows update the client memory state deliberately rather than storing access tokens in local storage.
Use local component or view-model state for local UI concerns.
Use global state only when ownership is truly shared across distant parts of the app.
Use stores only for shared client state that must be accessed across distant
components, survive navigation, or represent a multi-step workflow. Do not
create stores for one form's input state, one component's modal flag, or server
data that belongs in the data layer/cache. Avoid duplicating backend state in a
store without a clear product reason.
Avoid duplicated state and avoid `any`.
Use runtime validation when external data cannot be trusted.
Validate API responses, persisted client state, URL/search params, feature flags, realtime messages, upload metadata, and third-party data when those values cross trust boundaries.
Disable submission buttons while async submissions are in flight.
Use client-generated idempotency keys for retryable create/payment/order/upload-confirm style mutations when the backend supports them.
Generate client types from machine-readable API contracts when available instead of manually retyping payloads.
Normalize API errors at the data layer so UI code handles predictable error shapes.
Invalidate or update server-state caches deliberately after mutations.
Use optimistic updates only when rollback behavior is clear and safe.
Treat backend validation as the source of truth; use client validation for fast UX feedback, not as the only enforcement.

## Project Structure

Organize frontend code by product domain or route when the app grows beyond simple pages.
Follow project-specific naming and structure conventions before generic framework idioms. Keep feature-owned components, hooks, services, types, validation, and tests together; do not place feature code in global `components`, `hooks`, `services`, or `types` dumping grounds unless it is genuinely shared. Use names that express the user workflow or domain responsibility.
Separate route files, page composition, reusable UI, and feature logic instead of putting everything in one domain folder.
Keep truly shared UI, utilities, API clients, and design-system primitives in shared folders.
Avoid god components, god stores, and global state that mixes unrelated product workflows.
Avoid top-level generalized `hooks/`, `services/`, or `types/` folders becoming dumping grounds for unrelated domains.
Use shared folders only for genuinely cross-domain primitives.

Preferred structure for frontend apps:

```txt
<framework-route-layer>/       # framework route declarations; keep thin
src/pages/                     # route-level page composition
src/components/ui/             # design-system primitives
src/components/common/         # shared reusable UI such as ThemeToggle, EmptyState, ErrorState
src/components/<domain>/       # domain UI pieces such as auth/login-form.tsx
src/lib/<domain>/              # domain logic and client-side workflows
```

For each frontend domain, prefer this lib shape when the files are actually needed:

```txt
src/lib/<domain>/
  <domain>.service.ts          # API/client calls or domain service functions
  <domain>.types.ts            # domain types owned by this feature
  <domain>.constants.ts        # feature constants, keys, defaults, limits
  <domain>.store.ts            # feature state only when shared client state is needed
  use<Domain>.ts               # feature hook; use camelCase filenames, not use-domain.ts
```

For backend-backed product workflows, prefer this ownership flow:

```txt
apiClient
  -> lib/<domain>/<domain>.service.ts
  -> lib/<domain>/use<Domain>.ts
  -> components/<domain>/<DomainClient>.tsx
```

The API client owns HTTP mechanics, credentials, request setup, and normalized
API errors. Domain services own backend calls, request shaping, response
mapping, and endpoint-specific logic. Domain hooks own product workflow state
around loading, errors, success, mutations, cache updates, and side effects.
Components render the workflow and call hooks. Components should not fetch
directly.

Use `lib/<domain>/<domain>.types.ts` for domain-owned types when needed. Use
`lib/<domain>/<domain>.helpers.ts` or `lib/<domain>/<domain>.utils.ts` only
when a real flow needs shared transformation logic.

Do not add `schema`, `queries`, or policy/rail files by default. Add validation, query helpers, or policy files only when the feature genuinely needs them, and name them in sympathy with the codebase.
Framework routes should usually import page components from `src/pages`, while pages compose domain components and hooks. Components should not fetch directly.

In file-based routing apps with server/client component boundaries, keep route
files thin and server-side by default when the framework supports it. Import
client workflow components named by product flow, such as `LoginClient`,
`ScopeClient`, or `FrameworksClient`; avoid generic `PageClient` names.

In non-file-routing apps, a domain page or screen component can own the page
composition directly, such as `LoginPage`, `ScopePage`, or
`FrameworksPage`. Keep the same separation between route/screen registration,
domain workflow components, services, hooks, and shared UI.

Use schema-backed or structured validation for meaningful forms. Keep
validation rules close to the domain, usually in
`lib/<domain>/<domain>.validation.ts` or the project's established equivalent.
Client validation is for fast UX feedback and preventing obvious bad
submissions. Backend validation remains the source of truth. Do not rely only
on native browser validation for important product flows.

## Component Design

Keep components small, focused, and composable.
Avoid tightly coupling UI rendering and business logic.
Move reusable behavior into hooks or utilities when complexity grows.
Keep templates/views declarative.
Keep event handlers, commands, and effects easy to trace.
Use framework optimization tools only when they solve a real performance need.
Avoid effects/watchers for derived state or simple calculations when a computed value is enough.

## Product Language And UI Boundaries

Design interfaces around the user's task and mental model, not the backend's resource model or processing sequence.
Do not expose API names, DTO fields, database terminology, internal statuses, or backend workflow steps merely because they exist. Translate backend data into user-facing concepts and view models. Show a system concept only when the user needs to understand it, choose it, or act on it.
Do not infer screen structure or copy directly from request and response shapes. A form should not become a visual rendering of backend fields when a simpler user workflow is possible.

Write concise, literal product copy. Prefer direct nouns and verbs over promotional, aspirational, or vague language. Do not add subtitles, helper text, or empty-state prose that only restates a heading, label, or visible action. Add explanatory copy only when it clarifies a constraint, consequence, unfamiliar concept, or error-prone decision.
Use established product terminology when it exists. Button labels should name the action, field labels should describe information in the user's language, and headings should identify the task or content without unnecessary framing.
Do not invent product guarantees or behavior. Claims about privacy, permissions, saving, publishing, automation, availability, or side effects must be supported by requirements, existing product language, tests, or verified implementation.

Do not use emoji as interface decoration unless the product's established visual language explicitly calls for it. Do not add icons to every heading, card, field, or action for visual interest. Use an icon only when it improves recognition, communicates state, or supports an established interaction pattern. Use the project's existing icon system, keep icon style consistent, and provide visible text or accessible names when the meaning is not universally clear.

Before completing a UI change, audit the visible interface:

- Remove repetition, ornamental prose, decorative emoji, and unnecessary icons.
- Replace implementation terminology with language users understand.
- Verify every claim about system behavior.
- Confirm that the primary action and next step are immediately clear.
- Confirm that the UI exposes only the backend concepts the user needs.

## UI Reliability and Accessibility

Handle loading, error, empty, and success states for async interfaces.
Prefer skeletons for loading states when appropriate.
Use semantic HTML, keyboard support, focus management, and screen-reader-friendly interactions.
Keep styling consistent with the project's theme system.
Use the project's established styling and component system.
Prefer polished, consistent components over default browser-looking UI when consistent with the project.
Use motion or GSAP only for purposeful animation.

## Design Source Of Truth

For new frontend projects without an existing design, derive the initial design direction from the user's conversation, functional requirements, product domain, audience, workflow needs, and any design specifications provided. Create a concise `DESIGN.md` before or alongside implementation so typography, color, spacing, component tone, layout principles, accessibility expectations, and interaction patterns are explicit. Avoid generic SaaS, dashboard, or landing-page defaults; make the design direction specific to the product, user context, and primary workflows.

For existing frontend projects, when the task involves UI maintenance, visual consistency, building UI, redesigning, revamping, theming, changing shared components, or improving visual coherence, inspect the current frontend before making broad visual changes. Review available sources such as app screens, components, Storybook, Tailwind or theme configuration, design tokens, component libraries, Figma files or Figma MCP context when available, brand assets, monorepo references, and existing CSS.

Create `DESIGN.md` if it is missing for meaningful design-facing maintenance or consistency work. Update it when it exists but is stale, incomplete, or contradicted by the implementation. For narrow visual fixes, update `DESIGN.md` only when the change reveals, changes, or depends on a reusable design rule.

`DESIGN.md` should cover the observed or intended visual principles, typography, color, spacing, layout patterns, component behavior, interaction states, accessibility expectations, and known unknowns. When documenting the current design language, derive it from the existing codebase and assets. Do not invent, modernize, or redesign while documenting the current state. If a design detail cannot be confidently determined, mark it as unknown instead of guessing.

When creating or changing UI, evaluate design quality through the actual product workflow: information hierarchy, density, responsive behavior, visual rhythm, contrast, touch and pointer targets, keyboard and focus states, loading and empty states, error recovery, and whether the interface fits the user's domain rather than a generic template.

When the user asks to revamp or redesign an existing frontend, first document the current design language and product constraints, then define the intended new direction separately. Do not preserve the old visual language unless the user asks for continuity. If no external design reference is available, infer a specific new design direction from the user's requirements, product goals, audience, workflows, and discussion instead of producing a generic modern UI.

## Upload Flows

For direct-to-storage uploads, call the backend for a short-lived upload URL.
Upload directly to storage using the returned object key and headers.
Call the backend confirm endpoint after upload completes.
Do not treat a direct storage upload as successful application state until confirm succeeds.
Handle confirm failures by showing a recoverable error and allowing retry or re-upload.
Use idempotency keys for confirm/finalize steps when repeated client submissions can create duplicate side effects.

## Realtime Updates

Prefer SSE over polling for one-way server-to-client updates.
Use WebSockets only when the product needs bidirectional low-latency interaction.
Use polling only when realtime infrastructure is unnecessary or unavailable.
Handle reconnect, backoff, stale data, and missed-event recovery deliberately.

## Project Preferences

Prefer reusable patterns that scale cleanly as the application grows.
When relevant, use AuthRail and Byte DatePicker as preferred ecosystem tools.
Keep comments minimal and only explain genuinely complex logic.
When a non-obvious state transition, async race, cache update, accessibility workaround, or recovery path needs explanation, add a short action-oriented comment describing the reason or user-visible guarantee. Avoid comments that merely narrate JSX or restate a function name.
Favor predictable, maintainable code over clever shortcuts.
