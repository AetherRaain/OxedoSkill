---
name: oxedoskill
description: Highlord's fullstack engineering playbook. Use when building, debugging, or reviewing features in OxedoScans (React 19 + Vite + react-router + i18next frontend; Hono on Cloudflare Workers with D1/R2/KV/Workers AI backend) OR any React/TypeScript/Cloudflare project. Enforces test-driven development, root-cause debugging, verification-before-claims, and the project's RTL/auth/role conventions. Pulls in curated React, TypeScript, Cloudflare Workers, testing, security, and workflow references. Also carries optional polyglot references (Python 3.12+ with uv/ruff/pydantic, and systems programming in Rust/Go/C/C++) that stay dormant on JS/TS work and load when a future project uses those stacks.
---

# OxedoSkill — Highlord's Fullstack Playbook

One skill that carries how I want features built: the **discipline** (TDD, root-cause
debugging, verify-before-claiming), the **stack patterns** (React/Vite RTL, Hono on
Cloudflare Workers + D1), and the **OxedoScans project specifics**. Works in the
OxedoScans repo and travels to the next one.

> Distilled and curated from: [obra/superpowers](https://github.com/obra/superpowers)
> (workflow methodology), [jeffallan/claude-skills](https://github.com/jeffallan/claude-skills)
> (React/TS/fullstack technical skills), and wshobson's JS/TS plugin set. Python &
> systems-programming material was intentionally excluded — out of scope for this stack.
> Full attribution in [CREDITS.md](CREDITS.md).

## When to use

- Implementing a feature or fixing a bug in OxedoScans (or any React + TS + Cloudflare app).
- Before claiming work is "done", "fixed", or "passing".
- When a bug, test failure, or unexpected behavior appears.
- When reviewing a diff or planning a non-trivial change.

## The three iron laws (non-negotiable)

These come from the superpowers methodology. They are gates, not suggestions.

### 1. No production code without a failing test first (TDD)
Write the test → watch it fail for the right reason → write minimal code to pass → refactor.
If you wrote code before the test, delete it and start from the test. Exceptions (throwaway
prototypes, generated code, pure config) require asking the human first.
→ Detail: [references/testing.md](references/testing.md)

### 2. No fixes without root-cause investigation first (debugging)
Symptom-patching is failure. Before proposing ANY fix: read the full error/stack, reproduce
consistently, check recent changes, form a hypothesis, then fix the cause — not the symptom.
Especially under time pressure, when "one quick fix" looks obvious.
→ Detail: [references/workflow.md](references/workflow.md) §Systematic debugging

### 3. No completion claims without fresh verification evidence
"Tests pass" requires running the test command *in this turn* and reading 0 failures. Never
claim green from a previous run, intuition, or "should work". Run the command, read the exit
code, then state the claim **with** the evidence. If a step was skipped, say so.
→ Detail: [references/workflow.md](references/workflow.md) §Verification gate

## Default workflow for a non-trivial change

1. **Understand** — read the relevant code + `CLAUDE.md` + `PLAN.md`. Don't guess at structure.
2. **Plan** — for anything multi-file or risky, write the plan first (brainstorm → plan).
3. **Red** — write the failing test (`npm --prefix api test` for backend; component/e2e for UI).
4. **Green** — minimal code to pass.
5. **Refactor** — clean up while staying green; match surrounding style, comment density, naming.
6. **Verify** — run tests + build + typecheck; read the output. (`tsc --noEmit`, `npm run build`.)
7. **Review** — self-review the diff for bugs, reuse, security; consider `/code-review`.
8. **Finish** — commit with a clear message; push only when asked / per repo policy.

## Reference index (load on demand)

| Topic | File | Load when |
|---|---|---|
| OxedoScans operations & conventions | [references/oxedoscans-project.md](references/oxedoscans-project.md) | Working in the OxedoScans repo |
| React + Vite + RTL frontend | [references/react-frontend.md](references/react-frontend.md) | Building/editing UI |
| TypeScript + Hono + Workers + D1 | [references/backend-workers.md](references/backend-workers.md) | Building/editing API |
| Testing (Vitest, harness, TDD) | [references/testing.md](references/testing.md) | Writing or running tests |
| Security (auth, payments, limits) | [references/security.md](references/security.md) | Auth/money/user-input paths |
| Workflow (plan/debug/verify/review) | [references/workflow.md](references/workflow.md) | Any non-trivial task |
| Frontend UI engineering (a11y, CWV, polish) | [references/frontend-ui-engineering.md](references/frontend-ui-engineering.md) | Building production-quality UI |
| End-to-end testing (GWT + Playwright) | [references/e2e-testing.md](references/e2e-testing.md) | Writing/ debugging E2E flows |
| MCP companions & marketplaces | [references/mcp-and-marketplaces.md](references/mcp-and-marketplaces.md) | Wiring MCP / finding more skills |
| **Python** (uv/ruff/pydantic/async) — *polyglot* | [references/python.md](references/python.md) | A Python project (dormant on JS/TS) |
| **Systems** (Rust/Go/C/C++) — *polyglot* | [references/systems-programming.md](references/systems-programming.md) | A systems project (dormant on JS/TS) |

## Style rules (always)

- Write code that reads like the surrounding code — match its naming, comment density, idioms.
- RTL/LTR via logical CSS props; never hardcode left/right. No emojis in UI — Lucide icons.
- All frontend API calls go through `frontend/src/utils/api.js`. No bespoke `fetch`.
- Reference files as `path:line`. Keep diffs minimal and scoped to the task.
