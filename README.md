<div align="center">

# ⚡ OxedoSkill

### fullstack engineering playbook — as a Claude Code Skill

*One skill that carries the **discipline**, the **stack patterns**, and the **polyglot reach** —
so every feature gets built the same, high-quality way.*

<br/>

![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-ff3b54?style=for-the-badge&logo=anthropic&logoColor=white)
&nbsp;
![License](https://img.shields.io/badge/license-MIT-3ddc97?style=for-the-badge)

<br/>

![React](https://img.shields.io/badge/React-20232a?style=flat-square&logo=react&logoColor=61dafb)
![TypeScript](https://img.shields.io/badge/TypeScript-3178c6?style=flat-square&logo=typescript&logoColor=white)
![Cloudflare](https://img.shields.io/badge/Cloudflare_Workers-f38020?style=flat-square&logo=cloudflare&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776ab?style=flat-square&logo=python&logoColor=white)
![Rust](https://img.shields.io/badge/Rust-000000?style=flat-square&logo=rust&logoColor=white)
![Go](https://img.shields.io/badge/Go-00add8?style=flat-square&logo=go&logoColor=white)

</div>

---

> **OxedoSkill** is a curated [Claude Code Skill](https://docs.claude.com/en/docs/claude-code/skills):
> a single, opinionated playbook that makes Claude build like a senior engineer. It enforces a
> non-negotiable **workflow discipline**, carries deep **React / TypeScript / Cloudflare Workers**
> patterns, and keeps a **dormant polyglot layer** (Python, Rust/Go/C/C++) ready for the next repo.
> Distilled and curated from the best public skills — not blind-copied. Attribution in
> [`CREDITS.md`](./CREDITS.md).

<div align="center">

**[The 3 iron laws](#-the-three-iron-laws)** · **[What's inside](#-whats-inside)** · **[Install](#-install)** · **[Credits](#-credits)**

</div>

---

## ✦ The three iron laws

These are gates, not suggestions — the backbone of every task:

1. **No production code without a failing test first** *(TDD)* — red → green → refactor.
2. **No fixes without root-cause investigation first** *(debugging)* — symptom-patching is failure.
3. **No completion claims without fresh verification evidence** — run the command, read the output, *then* claim.

> Inspired by [obra/superpowers](https://github.com/obra/superpowers).

---

## ✦ What's inside

A focused `SKILL.md` (laws + default workflow + index) plus on-demand references:

| Reference | Covers |
|---|---|
| **react-frontend** | React 19 + Vite + RTL/LTR, state, data, theming |
| **frontend-ui-engineering** | Production-quality UI, accessibility, Core Web Vitals *(Addy Osmani)* |
| **backend-workers** | Hono on Cloudflare Workers, D1 patterns, auth, idempotency |
| **testing** | Vitest + a `node:sqlite` D1 harness, TDD, money/auth-path tests |
| **e2e-testing** | Given-When-Then + Playwright for critical flows *(bmad-labs)* |
| **security** | Auth, payments, rate limiting, abuse — the money-path lens |
| **workflow** | Plan → debug → verify → review → finish |
| **mcp-and-marketplaces** | Recommended MCP servers (GitHub, Scrapling) + where to find more skills |
| **python** *(polyglot, dormant)* | Python 3.12+ — uv, ruff, pydantic, type-safety, async, pytest |
| **systems-programming** *(polyglot, dormant)* | Rust / Go / C / C++ — ownership/RAII, memory safety, concurrency |

The polyglot references **stay dormant** on JS/TS work and wake up only in a Python/systems project —
so the skill stays sharp where you live now, and travels to whatever you build next.

---

## ✦ Install

**User-level (available in every project on your machine):**

```bash
git clone https://github.com/AetherRaain/oxedoskill.git ~/.claude/skills/oxedoskill
```

**Project-level (commit it with a repo, takes precedence there):**

```bash
git clone https://github.com/AetherRaain/oxedoskill.git .claude/skills/oxedoskill
```

Claude Code auto-discovers it on next launch. It activates on React/TS/Cloudflare work (and Python /
systems projects), or invoke it explicitly with `/oxedoskill`.

---

## ✦ Credits

Curated and distilled — with attribution — from:
[obra/superpowers](https://github.com/obra/superpowers) ·
[jeffallan/claude-skills](https://github.com/jeffallan/claude-skills) ·
[wshobson/agents](https://github.com/wshobson/agents) ·
[addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) ·
[bmad-labs/skills](https://github.com/bmad-labs/skills) ·
plus MCP picks ([github-mcp-server](https://github.com/github/github-mcp-server),
[scrapling](https://github.com/d4vinci/scrapling)) discovered via
[claudemarketplaces.com](https://claudemarketplaces.com). Full notes in [`CREDITS.md`](./CREDITS.md).

<div align="center">
<sub>Built for <a href="https://github.com/AetherRaain/OxedoScans">OxedoScans</a> · carried forward to every repo after. · MIT</sub>
</div>
