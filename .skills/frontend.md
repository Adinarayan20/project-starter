# Antigravity Skill: Frontend Architecture
# Priority: CRITICAL | Impact: 10/10 | Rating: ⭐⭐⭐⭐⭐

## ACTIVATION
Load when: building any UI, any component, any page, any route.
Apply to: React, Next.js, Vite — all frontend code.

---

## CORE RULE
> The user sees this. If this is broken, nothing else matters.
> Build for performance, accessibility, and zero layout shift from day one.

---

## TECH STACK (STANDARD)

### Framework
- Next.js 15 with App Router (preferred for public sites + SaaS)
- Vite + React (preferred for pure SPAs + dashboards)

### State Management
| State Type | Tool | Rule |
|---|---|---|
| Server/async state | TanStack Query | ALL data fetching goes here |
| Global client state | Zustand | Only for truly global UI state |
| Local UI state | useState / useReducer | Component-scoped state |
| Form state | React Hook Form + Zod | All forms, no exceptions |
| URL state | nuqs | Filters, pagination, search params |

### Styling
- CSS Modules for component-scoped styles (default)
- Tailwind CSS (only when design system is pre-configured)
- NEVER inline styles except for truly dynamic values

### Component Library
- Radix UI primitives (accessible, unstyled)
- shadcn/ui (Radix + Tailwind, copy-paste approach)

---

## ENFORCEMENT RULES

### Data Fetching
```
✅ Use TanStack Query (useQuery, useMutation) for ALL server state
✅ Use Suspense boundaries with proper loading fallbacks
✅ Use optimistic updates for all user mutations
✅ Prefetch data for predictable navigation flows
✅ Implement proper error boundaries (not just try/catch)

❌ NEVER fetch data in useEffect
❌ NEVER store server data in useState
❌ NEVER use Context API for server state
❌ NEVER fetch on the client what can be fetched on the server
```

### Performance
```
✅ React Server Components for all data-heavy pages
✅ Code-split every route (dynamic import)
✅ Lazy-load ALL components below the fold
✅ Use next/image or equivalent for all images
✅ Preload critical fonts with rel="preload"
✅ Implement virtual scrolling for lists > 100 items

Core Web Vitals Targets (CI/CD gate — must pass to deploy):
  LCP: < 2.5s
  CLS: < 0.1
  INP: < 200ms
  FCP: < 1.8s

❌ NEVER import * from large libraries (lodash, moment)
❌ NEVER use barrel re-exports on hot paths (bundle bloat)
❌ NEVER block rendering with synchronous heavy operations
```

### Accessibility
```
✅ All interactive elements keyboard-navigable
✅ All images have descriptive alt text
✅ All forms have associated labels
✅ Color contrast meets WCAG 2.2 AA minimum (4.5:1)
✅ Focus indicators visible on all interactive elements
✅ ARIA roles only when semantic HTML insufficient
❌ NEVER use div/span as buttons or links
```

### Component Rules
```
✅ Single responsibility — one component does one thing
✅ Props typed with TypeScript interfaces (never any)
✅ Default exports for page components, named exports for everything else
✅ Colocate component styles with component file
✅ Colocate component tests with component file

❌ NEVER pass more than 5 props without grouping into an object
❌ NEVER use index as key in dynamic lists
❌ NEVER mutate props or external state directly
```

---

## FOLDER STRUCTURE (FEATURE MODULE)
```
features/auth/
├── components/
│   ├── LoginForm.tsx
│   ├── LoginForm.test.tsx
│   └── LoginForm.module.css
├── hooks/
│   └── useAuth.ts
├── lib/
│   ├── auth-utils.ts
│   └── auth-schema.ts      # Zod validation schemas
├── types.ts
└── index.ts                 # Barrel: only export public API
```

---

## WORLD-CLASS STACK
- next.js: https://github.com/vercel/next.js (⭐ 131k)
- tanstack-query: https://github.com/TanStack/query (⭐ 44k)
- zustand: https://github.com/pmndrs/zustand (⭐ 50k)
- zod: https://github.com/colinhacks/zod (⭐ 36k)
- react-hook-form: https://github.com/react-hook-form/react-hook-form (⭐ 42k)
- radix-ui: https://github.com/radix-ui/primitives (⭐ 17k)
- shadcn-ui: https://github.com/shadcn-ui/ui (⭐ 82k)
- vite: https://github.com/vitejs/vite (⭐ 70k)
