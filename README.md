# Antigravity Starter Template

A world-class project starter template with 15 enterprise-grade AI skills built in.
Works in **Antigravity IDE**, **VS Code**, **GitHub Codespaces**, and **Cursor** — out of the box.

## What's Included

```
antigravity-starter/
├── .skills/                  ← 15 enterprise skills (travels with every project)
├── .github/
│   ├── copilot-instructions.md  ← GitHub Copilot reads these automatically
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── workflows/
│       └── ci.yml               ← Lint + test + build + Lighthouse CI
├── .devcontainer/
│   └── devcontainer.json        ← Codespaces: pre-configured environment
├── .env.example                 ← All environment variable slots documented
├── .gitignore                   ← Node.js / Next.js / Python comprehensive
└── README.md                    ← This file
```

## How to Use This Template

### Option A: GitHub Template (Recommended)
1. Push this repo to GitHub and mark it as a **Template Repository**
   (Settings → General → ✅ Template repository)
2. Every new project: click "Use this template" on GitHub
3. Clone your new repo — `.skills/` is already inside

### Option B: Local Copy
```powershell
# Windows (PowerShell)
Copy-Item -Path "C:\path\to\antigravity-starter" -Destination "C:\path\to\new-project" -Recurse
```

```bash
# Mac / Linux / Codespaces
cp -r ~/antigravity-starter ~/new-project
```

## Skills System

The `.skills/` folder contains 15 enterprise-grade knowledge files that AI tools
(Antigravity, Copilot, Cursor, Claude) read to enforce world-class standards.

| Skill | What It Enforces |
|---|---|
| `folder_structure.md` | Feature-first project architecture |
| `security.md` | Zero-trust, JWT in HttpOnly cookies only |
| `design_system.md` | HSL color tokens, fluid typography |
| `brand_originality.md` | Anti-AI-look: no generic purple gradients |
| `mobile_first.md` | 48px touch targets, dvh, 150kb JS budget |
| `frontend.md` | TanStack Query, Zustand, RSC rules |
| `backend.md` | Clean architecture, CQRS, Outbox pattern |
| `animation_motion.md` | transform+opacity only, Framer Motion, GSAP |
| `content_copywriting.md` | Landing page formula, AI copy blocklist |
| `ai_agentic.md` | RAG, tool-calling, context guardrails |
| `context_engineering.md` | LLM context budgets, compression, memory |
| `marketing_seo.md` | JSON-LD schema, Core Web Vitals, analytics |
| `algorithms.md` | Debounce, virtual scroll, circuit breaker |
| `flow_pipeline.md` | Blue-green deploy, BullMQ, OpenTelemetry |
| `documentation.md` | ADRs, changelog, contributing guide |

## Updating Skills

Skills are versioned in this repo. To get the latest:
```bash
# Pull latest skills from Antigravity IDE (Windows)
Copy-Item -Path "C:\Users\adina\.gemini\antigravity-ide\.skills\*" -Destination ".\.skills\" -Force
git add .skills/
git commit -m "chore(skills): update to latest Antigravity enterprise skills"
```

---

*Antigravity Starter Template · Enterprise Skills v2.0*
