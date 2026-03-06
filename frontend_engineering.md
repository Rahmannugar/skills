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

- `components/ui` contains reusable UI components.
- `components/shared` contains reusable domain components.
- `hooks` contains reusable logic hooks.
- `services` contains API service logic.
- `api` or `http` contains the API client.
- `stores` contains global state (Zustand or Context).
- `types` contains shared TypeScript types and schemas.
- `utils` contains shared helper utilities.
- `lib` contains integrations and configuration.

Avoid dumping logic into components.

---

### Data Fetching and API Layer

Do not fetch data directly inside components.

Use a structured data layer:

1. HTTP/API client
2. Services
3. Hooks

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

- Use **axios** for HTTP requests.
- Create a reusable **HTTP client instance**.
- API interactions must go through **services**.
- React components should only interact through **hooks**.

Use **React Query** for:

- caching
- background refetching
- request deduplication
- loading and error states

Avoid manual fetch state management.

---

### Types and Validation

Use **TypeScript for strict typing** across the application.

- Define shared types in `types/`.
- Use **Zod** when runtime validation is required.
- API responses should be typed.
- Avoid `any`.

Example:

- API schemas
- form validation
- external data validation

Types should be reusable and colocated when appropriate.

---

### State Management

Use the correct tool depending on scope.

Local state:

- `useState`
- `useReducer`

Server state:

- **React Query**

Global state:

- **Zustand**
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
- Follow **SOLID** and **DRY** principles.
- Respect existing component patterns in the codebase.

Avoid tightly coupling UI and business logic.

---

### Event Handlers

Do not place complex inline functions inside JSX.

Bad:

```tsx
<button onClick={() => doSomething(id, value)}>
```

Correct pattern:

```tsx
const handleClick = () => {
  doSomething(id, value);
};

<button onClick={handleClick} />;
```

Handlers should be defined above the return statement.

---

### React Optimization

Use React performance tools when they are appropriate.

Prioritize:

- `ref`
- `memo`
- `useMemo`
- `useCallback`
- `Suspense`

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

Structure: `components/ui/`

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
- use a global theme color system for consistent colors.

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

- **Authorization**: https://github.com/Rahmannugar/auth-rail
- **Datepicker**: https://github.com/Rahmannugar/byte-datepicker

Do not replace these tools with other libraries unless explicitly required.

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

-
