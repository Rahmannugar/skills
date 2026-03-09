## frontend_engineering

Build frontend systems that are scalable, maintainable, and production ready.
Respect existing project patterns and architecture.

The goal is to produce a clean, modular codebase that scales as the application grows.

---

### Architecture and Folder Structure

Follow clear separation of concerns and structured directories.

Typical structure should resemble:

```
src/
  app/ or pages/
  components/
    ui/
    shared/
  hooks/
  services/
  api/
  stores/
  types/
  utils/
  lib/
```

Guidelines:

- components/ui contains reusable UI components.
- components/shared contains reusable domain components.
- hooks contains reusable logic hooks.
- services contains API service logic.
- api or http contains the API client.
- stores contains global state (Zustand or Context).
- types contains shared TypeScript types and schemas.
- utils contains shared helper utilities.
- lib contains integrations and configuration.

Avoid dumping logic into components.

---

### Data Fetching and API Layer

Do not fetch data directly inside components.

Use a structured data layer:

- HTTP/API client
- Services
- Hooks

Example structure:

```
api/
  http-client.ts

services/
  user.service.ts
  product.service.ts

hooks/
  useUsers.ts
  useProducts.ts
```

Rules:

- Use axios for HTTP requests.
- Create a reusable HTTP client instance.
- API interactions must go through services.
- React components should only interact through hooks.

Use React Query for:

- caching
- background refetching
- request deduplication
- loading and error states

Avoid manual fetch state management.

Buttons should be disabled during submission to prevent duplicate requests or repeated actions.

---

### Types and Validation

Use TypeScript for strict typing across the application.

Define shared types in types/.

Use Zod when runtime validation is required.

API responses should be typed.

Avoid any.

Example:

- API schemas
- form validation
- external data validation

Types should be reusable and colocated when appropriate.

---

### State Management

Use the correct tool depending on scope.

Local state:

- useState
- useReducer

Server state:

- React Query

Global state:

- Zustand
- React Context when appropriate

Examples of global state:

- theme
- user session
- feature flags

Do not introduce global state unnecessarily.

---

### Component Design

Components must be:

- small
- focused
- composable

Rules:

- A component should never become overly long.
- Break large components into smaller units.
- Follow SOLID and DRY principles.
- Respect existing component patterns in the codebase.
- Avoid tightly coupling UI and business logic.

---

### Event Handlers and JSX Callbacks

Avoid writing inline or multi-line functions directly inside JSX.

JSX should remain declarative and free of complex logic.

Bad:

```tsx
<input onChange={(e) => setValue(e.target.value)} />
```

Bad:

```tsx
<form
  onSubmit={(e) => {
    e.preventDefault();
    handleSubmit();
  }}
>
```

Bad:

```tsx
<button onClick={() => doSomething(id)} />
```

Correct pattern:

```tsx
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  setValue(e.target.value);
};

const handleSubmitForm = (e: React.FormEvent<HTMLFormElement>) => {
  e.preventDefault();
  handleSubmit();
};

const handleClick = () => {
  doSomething(id);
};

<input onChange={handleChange} />
<form onSubmit={handleSubmitForm} />
<button onClick={handleClick} />
```

Guidelines:

- Define handler functions above the return statement.
- Use named handler functions such as handleSubmit, handleChange, handleClick, handleDelete.
- Avoid multi-line functions inside JSX.
- JSX should reference handlers rather than contain logic.
- Keep JSX focused on rendering, not execution logic.

This improves readability, maintainability, and debugging.

---

### React Optimization

Use React performance tools when they are appropriate.

Prioritize:

- ref
- memo
- useMemo
- useCallback
- Suspense

Do not use them prematurely.
Optimize only when necessary for performance or rendering stability.

---

### useEffect Usage

useEffect must be used sparingly.
Use it only when it is the best or necessary option.

Avoid using it for:

- derived states
- simple calculations
- logic that belongs in event handlers

Prefer:

- React Query
- memoization
- derived values

---

### UI Component System

Use either:

- shadcn UI
- or custom components

Structure: components/ui/

Do not rely on default styles.
Customize components to match the application's design system.
Avoid generic UI libraries and template-like design systems.

---

### Styling

Use TailwindCSS as the primary styling system.

Guidelines:

- prefer utility-first styling
- extract reusable UI components when patterns repeat
- avoid unnecessary CSS files
- use a global theme color system for consistent colors

Avoid hardcoding random colors across components.

---

### Typography

Avoid overused or generic fonts.
Use a deliberate typography system that aligns with the product identity.

---

### Icons

Avoid generic icon overuse.
Icons should match the design language of the product.
Prefer consistent icon sets and purposeful usage.

---

### UI Copy and Product Language

Do not invent UI copy or product language.

Labels, headings, button text, and UI descriptions must come from:

- the provided requirements
- existing terminology already used in the codebase

Avoid fabricating feature names or branded UI terms.

For example, if the application is called Agently, do not invent UI labels such as:

- "Agently Flow"
- "Agently Secure Link"
- "Agently Dashboard"

unless those exact terms already exist in the codebase or requirements.

If UI copy is not provided, prioritize correct UI structure rather than inventing product language.

---

### UI State Handling

Components that depend on asynchronous data must handle all UI states:

- loading
- error
- empty
- success

Do not assume data is always available.

Use React Query state indicators rather than creating manual loading state logic.

---

### Loading States

When data is loading, use skeleton components rather than generic loaders or spinners.

Skeletons should match the layout of the content being loaded so that the UI structure remains stable during loading.

Avoid showing blank screens while data loads.

Use reusable skeleton components where appropriate.

---

### Error States

When requests fail, display clear and user-friendly error feedback.

Error messages should:

- be concise
- explain the problem when possible
- provide a retry option when appropriate

Avoid exposing raw server errors directly to the UI.

---

### Empty States

When data returns successfully but contains no results, render a meaningful empty state.

Empty states should explain the situation clearly and guide the user toward the next action if applicable.

Avoid showing empty containers without context.

---

### User Action Feedback

User-triggered operations must provide clear feedback.

Examples include:

- creating data
- updating data
- deleting data
- submitting forms
- triggering workflows

Use toast notifications for operation feedback.

Examples:

- success notifications
- failure notifications
- confirmation messages

Toasts should be:

- concise
- informative
- consistent across the application

Avoid silent failures or actions without visible feedback.

---

### Mutation Handling

Operations that modify data should use React Query mutations.

Mutation flows should include:

- optimistic updates when appropriate
- success toasts
- error toasts
- automatic query invalidation when needed

Avoid manually refetching data when React Query invalidation can handle it.

---

### React Query Query Keys

React Query queries must use consistent and predictable query keys.

Query keys should follow a structured pattern that reflects the resource being fetched.

Example patterns:

- ["users"]
- ["users", userId]
- ["orders"]
- ["orders", orderId]
- ["orders", { page, filters }]

Guidelines:

- Use array-based query keys rather than string keys.
- Query keys should clearly represent the data being fetched.
- Use consistent naming across services, hooks, and components.
- Avoid duplicating query keys for the same resource.

---

### Query Invalidation

When mutations modify server data, the corresponding queries must be invalidated.

Mutation success handlers should invalidate only the relevant queries.

Avoid broad invalidation such as clearing all queries when only one resource changes.

Prefer targeted invalidation that matches the query key structure.

---

### Data Ownership

Components should not call React Query directly for complex logic.

Data access should follow the pattern:

- service → hook → component

Hooks should encapsulate:

- query logic
- mutation logic
- cache invalidation
- optimistic updates

Components should only consume hooks and render UI.

---

### Derived UI State

Avoid duplicating server data into local component state.

React Query already manages server state.

Use derived values from query results instead of copying data into useState.

---

### Accessibility

Accessibility is a default requirement, not an optional enhancement.

Include:

- semantic HTML
- ARIA attributes when necessary
- keyboard navigation
- focus states
- accessible labels
- screen reader support

---

### Animations

Use:

- motion
- or GSAP

Animations should be purposeful and not excessive.

Sources for inspiration:

- 21st.dev
- React Bits

Do not blindly copy animated templates.

---

### Routing

Routes should be structured and organized.
Use index files where appropriate to simplify imports.
Pages or routes should follow a predictable structure.
Avoid deeply nested routing without clear purpose.

---

### Custom Tools

Use the following project tools when relevant:

- Authorization
  https://github.com/Rahmannugar/auth-rail

- Datepicker
  https://github.com/Rahmannugar/byte-datepicker

Do not replace these tools with alternative libraries unless explicitly required.

---

### Code Quality

Follow modern ES6+ conventions.

Use:

- linters
- consistent formatting
- consistent imports

Respect existing project conventions and patterns.

---

### Comments

Comments should be minimal, especially inside JSX.
Avoid cluttering rendered UI code with comments.
Use comments only when they clarify complex logic.

---

### Scalability

Every implementation should consider long-term maintainability.

The codebase must remain:

- modular
- readable
- predictable

Avoid shortcuts that create technical debt.
Prefer clear architecture and reusable patterns.
