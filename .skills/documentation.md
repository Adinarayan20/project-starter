# Antigravity Skill: Documentation & Knowledge Management
# Priority: STANDARD | Impact: 6.5/10 | Rating: ⭐⭐⭐½

## ACTIVATION
Load when: starting a new project, making architectural decisions, or building a public API.

---

## CORE RULE
> Code is written once, read a thousand times.
> The best documentation is code that doesn't need explanation — but that standard is unreachable.
> Document decisions (WHY), not implementations (WHAT the code already shows).

---

## REQUIRED DOCUMENTATION ARTIFACTS

```
Every project must have ALL of these at root level:

README.md         ← Project overview, setup, and quickstart
ARCHITECTURE.md   ← System design, service map, data flow diagrams
CONTRIBUTING.md   ← How to contribute (branching, PRs, review process)
SECURITY.md       ← Vulnerability disclosure, security contact
CHANGELOG.md      ← Version history (what changed, why, who)
.env.example      ← All required environment variables with descriptions
adr/              ← Architecture Decision Records (one .md per decision)
```

---

## README.md TEMPLATE

```markdown
# Project Name

One sentence that describes what this project does and who it's for.

## Quick Start

\`\`\`bash
git clone [repo]
cd [project]
cp .env.example .env      # Fill in your values
pnpm install
pnpm dev                  # Starts at http://localhost:3000
\`\`\`

## Requirements

- Node.js 22+
- PostgreSQL 16+
- Redis 7+

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| DATABASE_URL | ✅ Yes | PostgreSQL connection string |
| REDIS_URL | ✅ Yes | Redis connection string |
| NEXTAUTH_SECRET | ✅ Yes | Random string for session signing |
| OPENAI_API_KEY | ✅ Yes | OpenAI API key for AI features |
| STRIPE_SECRET_KEY | ⬜ Optional | Required for payments feature |

## Project Structure

\`\`\`
src/
├── app/          Next.js App Router pages
├── features/     Feature-first modules
├── components/   Shared UI components
└── lib/          Shared utilities
\`\`\`

## Tech Stack

- **Framework:** Next.js 15 (App Router)
- **Database:** PostgreSQL + Prisma ORM
- **Auth:** Auth.js v5
- **Styling:** Tailwind CSS + shadcn/ui
- **Deployment:** Vercel

## Running Tests

\`\`\`bash
pnpm test:unit        # Vitest unit tests
pnpm test:e2e         # Playwright end-to-end tests
pnpm test:coverage    # Coverage report
\`\`\`

## License

MIT
```

---

## ARCHITECTURE DECISION RECORDS (ADRs)

Every significant architectural decision must be documented as an ADR.

### When to Write an ADR
```
Write an ADR when you:
  ✅ Choose a framework, library, or tool over alternatives
  ✅ Change the database schema in a backward-incompatible way
  ✅ Choose a deployment strategy
  ✅ Adopt a new pattern (e.g., switching from REST to tRPC)
  ✅ Make a security policy decision
  ✅ Choose NOT to use a popular technology and why
```

### ADR Template
```markdown
# ADR-001: Use tRPC for Internal APIs

**Date:** 2026-06-04
**Status:** Accepted
**Deciders:** [Name, Name]

## Context

We need a communication layer between our Next.js frontend and backend.
Options considered: REST with OpenAPI, GraphQL, tRPC.

## Decision

We will use tRPC for all internal API communication between the frontend and backend.

## Rationale

- **Type safety:** End-to-end TypeScript types without code generation
- **Developer experience:** Autocomplete for API calls in the frontend
- **Performance:** No over-fetching — client requests only needed fields
- **Simplicity:** No schema definition language — types are the schema

## Consequences

**Positive:**
- Eliminates entire category of type mismatch bugs
- Faster development (no API client generation step)

**Negative:**
- Tightly couples frontend and backend (acceptable for monorepo)
- Harder to expose public API to third parties (use REST adapter if needed)

## Alternatives Rejected

- REST/OpenAPI: Type safety requires code generation step (extra complexity)
- GraphQL: Overkill for internal APIs, more complex to set up and secure
```

### ADR File Naming
```
adr/
├── 001-use-nextjs-app-router.md
├── 002-choose-postgres-over-mysql.md
├── 003-adopt-trpc-for-internal-apis.md
├── 004-use-bullmq-for-background-jobs.md
└── 005-adopt-feature-first-folder-structure.md
```

---

## CHANGELOG FORMAT (Keep a Changelog)

```markdown
# Changelog

All notable changes to this project will be documented in this file.
Format: https://keepachangelog.com
Versioning: https://semver.org

## [Unreleased]

## [1.2.0] - 2026-06-04

### Added
- User avatar upload with automatic WebP conversion
- Dark mode persistence via cookie (no flash on page load)

### Changed
- Improved search performance from O(n) to O(log n) with DB index
- Migrated from Moment.js to date-fns (reduces bundle by 67%)

### Fixed
- Mobile nav drawer closing on outside click (iOS Safari fix)
- Payment webhook duplicate processing (added idempotency key)

### Security
- Updated dependencies to patch CVE-2026-XXXXX (high severity)

## [1.1.0] - 2026-05-20
...
```

---

## API DOCUMENTATION STANDARDS

### OpenAPI / Swagger (REST APIs)
```typescript
// Document every endpoint with JSDoc + openapi decorators (NestJS example)
/**
 * @swagger
 * /users/{id}:
 *   get:
 *     summary: Get user by ID
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema:
 *           type: string
 *           format: uuid
 *     responses:
 *       200:
 *         description: User found
 *         content:
 *           application/json:
 *             schema:
 *               $ref: '#/components/schemas/User'
 *       404:
 *         description: User not found
 */
```

### Code Comments (What NOT to Comment)
```typescript
// ❌ BAD — commenting the obvious
// Increment count by 1
count++;

// ✅ GOOD — explaining WHY (not WHAT)
// We use optimistic updates here to avoid blocking the UI while the
// server processes. The rollback handler in onError handles failures.
const [optimisticItems, addOptimistic] = useOptimistic(items);

// ✅ GOOD — explaining non-obvious business logic
// Stripe requires amount in smallest currency unit (cents for USD)
// 29.99 USD → 2999
const amountInCents = Math.round(price * 100);

// ✅ GOOD — explaining workarounds
// iOS Safari doesn't support dvh in @keyframes, so we use a JS approach
// See: https://bugs.webkit.org/show_bug.cgi?id=XXXXX
```

---

## CONTRIBUTING.md TEMPLATE

```markdown
# Contributing Guide

## Branching Strategy

\`\`\`
main        ← Production. Only merged from release branches.
develop     ← Staging. All PRs merge here.
feature/*   ← Feature branches (e.g., feature/user-avatar-upload)
fix/*       ← Bug fix branches
chore/*     ← Non-functional changes (deps, docs, refactor)
\`\`\`

## PR Requirements

All PRs must:
- [ ] Pass all CI checks (lint, typecheck, tests, build)
- [ ] Have a clear title following Conventional Commits format
- [ ] Include a description of WHAT changed and WHY
- [ ] Have at least one reviewer approval
- [ ] Not exceed 400 lines of changes (split large PRs)

## Commit Message Format (Conventional Commits)

\`\`\`
type(scope): short description

Types: feat, fix, chore, docs, style, refactor, test, perf
\`\`\`

Examples:
\`\`\`
feat(auth): add Google OAuth sign-in
fix(payments): handle Stripe webhook duplicate events
perf(search): add database index on products.category_id
docs(api): add OpenAPI schema for /users endpoint
\`\`\`
```

---

## WORLD-CLASS REFERENCES
- docusaurus: https://github.com/facebook/docusaurus (⭐ 58k)
- nextra: https://github.com/shuding/nextra (⭐ 13k)
- swagger-ui: https://github.com/swagger-api/swagger-ui (⭐ 27k)
- conventional-commits: https://www.conventionalcommits.org
- keep-a-changelog: https://keepachangelog.com
- adr-tools: https://github.com/npryce/adr-tools (⭐ 3k)
