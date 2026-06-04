# Antigravity Skill: Folder Structure & Project Architecture
# Priority: CRITICAL | Impact: 10/10 | Rating: ⭐⭐⭐⭐⭐

## ACTIVATION
Load this skill FIRST before any code is written or any file is created.
Apply to: every project, every time, no exceptions.

---

## CORE RULE
> Group by Feature/Domain — NEVER by file type.
> Wrong: /components + /hooks + /utils at root
> Right:  /features/auth + /features/dashboard + /features/billing

---

## CANONICAL PROJECT STRUCTURE

### Monorepo (Enterprise / Multi-App)
```
project-root/
├── .github/                    # CI/CD workflows, PR templates, issue templates
│   ├── workflows/
│   │   ├── ci.yml              # Lint, test, build on every PR
│   │   ├── deploy-staging.yml
│   │   └── deploy-production.yml
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── ISSUE_TEMPLATE/
├── .skills/                    # Antigravity skill reference directory (THIS FOLDER)
├── apps/                       # Deployable applications
│   ├── web/                    # Next.js / Vite public frontend
│   ├── admin/                  # Internal dashboard
│   └── api-gateway/            # GraphQL / REST gateway
├── services/                   # Domain microservices
│   ├── auth-service/
│   ├── payment-service/
│   └── ai-agent-service/
├── packages/                   # Shared internal libraries (npm workspaces)
│   ├── ui-core/                # Component library + design tokens
│   ├── db-client/              # Prisma/Drizzle shared schema + connection
│   ├── security-utils/         # JWT, encryption, rate limiter helpers
│   ├── telemetry/              # OpenTelemetry config, logging
│   └── types/                  # Shared TypeScript types
├── infrastructure/             # Infrastructure as Code
│   ├── terraform/              # AWS/GCP resource definitions
│   └── docker/                 # Optimized multi-stage Dockerfiles
├── docs/                       # Architecture docs, ADRs, runbooks
│   └── adr/                    # Architecture Decision Records
├── pnpm-workspace.yaml         # Workspace package definitions
├── turbo.json                  # Turborepo pipeline config
└── README.md
```

### Single App (Next.js / Vite)
```
src/
├── app/                        # Next.js App Router pages and layouts
│   ├── (marketing)/            # Route group: public landing pages
│   ├── (dashboard)/            # Route group: authenticated app
│   └── api/                    # API route handlers
├── features/                   # Feature-first modules
│   ├── auth/
│   │   ├── components/         # UI components specific to auth
│   │   ├── hooks/              # Custom hooks for auth feature
│   │   ├── lib/                # Auth utilities and helpers
│   │   ├── types.ts            # Auth-specific TypeScript types
│   │   └── index.ts            # Barrel export (public API of feature)
│   ├── billing/
│   └── dashboard/
├── components/                 # Shared UI components (used across features)
│   ├── ui/                     # Design system primitives
│   └── layout/                 # Layout components (Header, Footer, Sidebar)
├── lib/                        # Shared utilities (not feature-specific)
│   ├── api.ts                  # API client setup
│   ├── db.ts                   # Database connection
│   └── utils.ts                # Pure utility functions
├── styles/                     # Global CSS, design tokens
├── types/                      # Global TypeScript types
└── config/                     # Environment config, constants
```

---

## ENFORCEMENT RULES

```
✅ ALWAYS group by feature/domain, not by file type
✅ ALWAYS use barrel files (index.ts) for clean public API surfaces
✅ ALWAYS colocate tests with source files (auth.test.ts next to auth.ts)
✅ ALWAYS put infrastructure/IaC in dedicated /infrastructure or /infra folder
✅ ALWAYS put environment config in /config, never scattered in source
✅ ALWAYS have a /docs or /adr folder for architecture decisions

❌ NEVER put business logic in /pages or route handlers
❌ NEVER mix app-specific code with shared library code
❌ NEVER put all tests in a root /tests folder
❌ NEVER import from sibling feature folders (use packages instead)
❌ NEVER commit .env files (use .env.example with empty values)
```

---

## NAMING CONVENTIONS

```
Files:         kebab-case.ts          (user-profile.ts)
Components:    PascalCase.tsx         (UserProfile.tsx)
Hooks:         camelCase with use     (useUserProfile.ts)
Constants:     SCREAMING_SNAKE        (MAX_RETRY_COUNT)
Types:         PascalCase             (UserProfile, ApiResponse)
CSS Modules:   kebab-case.module.css  (user-profile.module.css)
Env vars:      SCREAMING_SNAKE        (DATABASE_URL, NEXT_PUBLIC_API_URL)
```

---

## WORLD-CLASS REFERENCES
- turborepo: https://github.com/vercel/turborepo (⭐ 26k)
- nx: https://github.com/nrwl/nx (⭐ 24k)
- bulletproof-react: https://github.com/alan2207/bulletproof-react (⭐ 30k)
- nodebestpractices: https://github.com/goldbergyoni/nodebestpractices (⭐ 103k)
