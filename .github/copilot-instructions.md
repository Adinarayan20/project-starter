# GitHub Copilot Instructions
# This file is automatically read by GitHub Copilot in VS Code and Codespaces.
# It tells Copilot to apply the Antigravity enterprise skills on every suggestion.

You are an expert full-stack engineer working in this repository.
Before generating any code, UI, copy, or architecture decisions, you MUST
apply the rules defined in the `.skills/` directory at the root of this project.

## Skill Loading Order

Always load and apply skills in this sequence:

### Phase 1 — Foundation (apply to EVERY file you touch)
- `.skills/folder_structure.md` — feature-first architecture, naming conventions
- `.skills/security.md` — zero-trust: JWT in HttpOnly cookies, RLS, secret management
- `.skills/design_system.md` — HSL token system, 8pt grid, fluid typography

### Phase 2 — Identity (apply to ALL public-facing UI)
- `.skills/brand_originality.md` — BLOCK all AI-look patterns (purple gradients, generic copy)
- `.skills/mobile_first.md` — mobile designed first, 48px touch targets, dvh units

### Phase 3 — Build
- `.skills/frontend.md` — TanStack Query for all fetching, Zustand for global state
- `.skills/backend.md` — Clean architecture, Repository pattern, CQRS
- `.skills/algorithms.md` — O(n) budgets, debounce, virtual scroll, circuit breaker

### Phase 4 — Experience (apply to all public pages)
- `.skills/animation_motion.md` — only animate transform+opacity, prefers-reduced-motion
- `.skills/content_copywriting.md` — 8-section landing formula, AI copy blocklist

### Phase 5 — AI Layer (apply when building AI features)
- `.skills/ai_agentic.md` — RAG, tool-calling, semantic caching, guardrails
- `.skills/context_engineering.md` — context budgets, compression, memory architecture

### Phase 6 — Growth & Ops
- `.skills/marketing_seo.md` — JSON-LD schema, Core Web Vitals CI gate
- `.skills/flow_pipeline.md` — CI/CD, blue-green deploy, BullMQ, OpenTelemetry
- `.skills/documentation.md` — ADR format, changelog, contributing guide

## Critical Rules (Never Violate)

1. **Brand:** No generic AI colors (#7c3aed purple, #1a1a1a black). Build from brand mood.
2. **Mobile:** Write CSS mobile-first (base → min-width). Use dvh not vh. Min 48px touch targets.
3. **Security:** JWT ONLY in HttpOnly Secure SameSite=Strict cookies. Never localStorage.
4. **Animations:** ONLY animate `transform` and `opacity`. Never width/height/margin.
5. **Copy:** No AI clichés. See `.skills/content_copywriting.md` FIND→REPLACE list.
6. **Colors:** ALL colors as HSL CSS custom properties. Never hardcoded hex in components.

## Before Writing Any Code

Ask yourself:
- Does this respect the folder structure defined in `.skills/folder_structure.md`?
- Does this UI element meet the mobile-first rules in `.skills/mobile_first.md`?
- Does this design look like it could be from any AI template? If yes, apply `.skills/brand_originality.md`.
- Does this animation only use transform + opacity? If not, fix it.
- Is any secret being stored in code or localStorage? If yes, fix it.
