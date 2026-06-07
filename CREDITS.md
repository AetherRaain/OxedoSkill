# Credits & sources

OxedoSkill is a **curated, distilled** playbook — not a verbatim copy of any source. It encodes
Highlord's OxedoScans project knowledge plus best-practice patterns adapted from the skills below.
Where these sources are installed as plugins, prefer their full skills for deep dives; OxedoSkill is
the opinionated, stack-specific layer on top.

## Sources distilled

- **[obra/superpowers](https://github.com/obra/superpowers)** — workflow methodology. Source of the
  three iron laws (TDD, systematic debugging, verification-before-completion) and the
  brainstorm→plan→execute→review→finish flow. MIT-style open source by Jesse Vincent.
  Skills referenced: `test-driven-development`, `systematic-debugging`,
  `verification-before-completion`, `writing-plans`, `brainstorming`, `requesting/receiving-code-review`.

- **[jeffallan/claude-skills](https://github.com/jeffallan/claude-skills)** (a.k.a. the
  "fullstack-dev-skills" plugin) — 66 fullstack technical skills. Source of the React/TypeScript/
  API/testing patterns. MIT. Skills referenced: `react-expert`, `typescript-pro`, `javascript-pro`,
  `api-designer`, `test-master`, `playwright-expert`, `secure-code-guardian`, `security-reviewer`,
  `sql-pro`, `websocket-engineer`, `fullstack-guardian`, `code-reviewer`.

- **wshobson JS/TS plugin set** — JavaScript/TypeScript commands & agents. Reinforced the TS-first
  and Node tooling patterns.

- **wshobson `python-development` plugin** (`wshobson/agents/plugins/python-development`) — agents
  `python-pro`, `fastapi-pro`, `django-pro` + 16 skill modules (uv, ruff, type-safety, async, testing,
  packaging, error-handling, performance, …). Distilled into `references/python.md`.

- **wshobson `systems-programming` plugin** (`wshobson/agents/plugins/systems-programming`) — agents
  `rust-pro`, `golang-pro`, `cpp-pro`, `c-pro` + skills (memory-safety-patterns, go-concurrency-patterns,
  rust-async-patterns). Distilled into `references/systems-programming.md`.

## Marketplace picks (claudemarketplaces.com — added 2026-06-07)

Selected 1–2 per category from the directory and distilled into references:

- **Skill — `addyosmani/agent-skills → frontend-ui-engineering`** (Addy Osmani) → distilled into
  `references/frontend-ui-engineering.md` (production-quality UI, accessibility, Core Web Vitals).
  https://github.com/addyosmani/agent-skills
- **Skill — `bmad-labs/skills → typescript-e2e-testing`** → methodology (Given-When-Then, real-infra,
  flaky-test debugging) distilled into `references/e2e-testing.md`, paired with Playwright for the
  React app. https://github.com/bmad-labs/skills
- **MCP — `github/github-mcp-server`** and **`d4vinci/scrapling`** → recommended + wiring notes in
  `references/mcp-and-marketplaces.md`.
- **Marketplaces — `davila7/claude-code-templates`**, `addyosmani/agent-skills`, `bmad-labs/skills`
  → recorded as sources for future picks in `references/mcp-and-marketplaces.md`.

## Polyglot layer (dormant by default)

The Python and systems-programming references are kept **out of the main triggering flow** so they
don't dilute OxedoScans (React/TS + Cloudflare) work — they load only when a project actually uses
those stacks. This keeps OxedoSkill sharp here while carrying the knowledge forward to a future repo.

## Maintaining this skill

- Keep `references/oxedoscans-project.md` in sync with `CLAUDE.md` / `PLAN.md` as the project evolves.
- This skill lives at the **user level** (`~/.claude/skills/oxedoskill/`) so it applies to OxedoScans
  and the next repo. To scope it to a single repo instead, copy it into that repo's
  `.claude/skills/oxedoskill/`.
- To extend or eval it, use the `skill-creator` skill.
