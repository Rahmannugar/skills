## frontend_engineering

Build frontend systems that are scalable, maintainable, and production ready.

Respect existing project patterns and architecture.

The goal is to produce a clean, modular codebase that scales as the application grows.

---

### Architecture and Folder Structure

Follow clear separation of concerns and structured directories.

Typical structure:

```
src
  app / pages
  components
    ui
    shared
  hooks
  services
  api
  stores
  types
  utils
  lib
```

Avoid dumping logic into components.

---

### File and Module Size

Avoid creating excessively large files.

Large frontend files often indicate:

- multiple responsibilities inside one component
- tightly coupled UI logic
- difficult-to-maintain rendering flows

Prefer extracting:

- smaller components
- reusable hooks
- shared utilities.

---

### Data Fetching and API Layer

Do not fetch data directly inside components.

Use structured layers:

- HTTP client
- Services
- Hooks

Use axios for HTTP requests.

Use React Query for:

- caching
- refetching
- deduplication
- loading and error states.

Avoid manual fetch state management.

Buttons should be disabled during submission.

---

### Types and Validation

Use TypeScript for strict typing.

Define shared types in types.

Use Zod when runtime validation is required.

Avoid any.

---

### State Management

Local state:

- useState
- useReducer

Server state:

- React Query

Global state:

- Zustand
- React Context when appropriate.

Avoid unnecessary global state.

---

### Component Design

Components must be:

- small
- focused
- composable

Follow SOLID and DRY principles.

Avoid tightly coupling UI and business logic.

---

### Component Size and Responsibility

Components must represent a single UI responsibility.

Large components often hide:

- mixed rendering logic
- complex conditional flows
- hidden state bugs

If a component grows too large, extract:

- smaller components
- hooks
- utilities.

---

### Event Handlers and JSX Callbacks

Avoid inline functions in JSX.

Define handlers above the return statement.

JSX should remain declarative and free of logic.

---

### React Optimization

Use:

- ref
- memo
- useMemo
- useCallback
- Suspense

Only optimize when necessary.

---

### useEffect Usage

useEffect must be used sparingly.

Avoid using it for:

- derived state
- simple calculations
- logic that belongs in event handlers.

Prefer derived values and React Query.

---

### UI Component System

Use:

- shadcn UI
- or custom components.

Avoid default styles.

Customize components to match the design system.

---

### Styling

Use TailwindCSS.

Avoid random colors.

Use a consistent theme system.

---

### UI State Handling

All async UI must support:

- loading
- error
- empty
- success states.

Use skeleton components for loading.

---

### React Query Query Keys

Use structured query keys.

Example:

```
["users"]
["users", userId]
["orders", { page }]
```

Avoid duplicate keys.

---

### Accessibility

Accessibility is required.

Include:

- semantic HTML
- keyboard navigation
- focus states
- screen reader support.

---

### Animations

Use:

- motion
- GSAP.

Animations should be purposeful.

---

### Custom Tools

Use:

- AuthRail
  https://github.com/Rahmannugar/auth-rail

- Byte DatePicker
  https://github.com/Rahmannugar/byte-datepicker

---

### Code Quality

Use:

- linters
- consistent formatting
- consistent imports.

---

### Comments

Keep comments minimal.

Only explain complex logic.

---

### Scalability

Code must remain:

- modular
- readable
- predictable

Avoid technical debt.

Prefer reusable patterns.
