# MCP companions & skill marketplaces

MCP servers aren't part of a skill file — they're live tool connectors. This reference records the
**recommended MCP servers** that pair with OxedoSkill's work, plus **where to get more skills/plugins**.
Curated from claudemarketplaces.com (skills · MCP · marketplaces).

## Recommended MCP servers

### github/github-mcp-server  — *developer-tools*
The official GitHub MCP. Operate repos/PRs/issues/Actions from the session instead of shelling out.
- **Why for this workflow:** OxedoScans is published to GitHub and we push after every change; this
  turns PR/issue/release work into direct tool calls.
- **Wire it:** add to the project's `.mcp.json` (or user config) with a GitHub token (repo scope).
  Repo: https://github.com/github/github-mcp-server
- **Pairs with:** the push-after-each-update workflow; `/code-review --comment` for inline PR review.

### d4vinci/scrapling  — *web-browser / scraping*
Adaptive web-scraping framework with a **built-in MCP server** for AI-assisted scraping. Stealth
fetching, Scrapy-like spiders, adaptive element relocation when sites change, anti-bot bypass.
- **Why for this project:** directly relevant to OxedoScans' scraper/import path and the open
  **"page images: uploads vs scraped URLs"** decision. If scraping is chosen, this is the engine —
  resilient selectors + concurrency beat brittle hand-rolled fetchers.
- **Caveat:** scraping copyrighted manga ties into the Tier 0 content/legal decision — use deliberately.
  Repo: https://github.com/d4vinci/scrapling

> To add an MCP server: put it in `.mcp.json` (project) or the user MCP config, supply credentials as
> env/secrets (never hardcode), then call its tools like any other. See the `cloudflare` and
> `workers-best-practices` skills for binding/secret patterns.

## Recommended skill / plugin marketplaces (sources for more)

- **davila7/claude-code-templates** (aitmpl.com) — one of the largest Claude Code marketplaces:
  components, skills, MCP configs, commands. Good first stop for a new capability.
  https://github.com/davila7/claude-code-templates
- **addyosmani/agent-skills** (Addy Osmani) — high-quality engineering skills (frontend-ui-engineering,
  performance-optimization, code-review-and-quality, security-and-hardening, git-workflow).
  Source of `frontend-ui-engineering.md`. https://github.com/addyosmani/agent-skills
- **bmad-labs/skills** — TypeScript-focused (e2e/unit testing, clean-code, mcp-builder). Source of the
  E2E methodology in `e2e-testing.md`. https://github.com/bmad-labs/skills
- **obra/superpowers** — workflow methodology (already the backbone of OxedoSkill's iron laws).
- **wshobson/agents** — large agent+skill set incl. the Python/systems plugins distilled here.
- Browse more at **claudemarketplaces.com** (skills · mcp · marketplaces, by category).

## How to "infuse" a new pick into OxedoSkill later

1. Read the source SKILL.md / README from its GitHub repo (don't blind-copy).
2. Distill the genuinely useful, stack-relevant patterns into a focused `references/<topic>.md`.
3. Add it to the SKILL.md reference index; credit the source in `CREDITS.md`.
4. Keep off-stack material dormant (load-only-when-relevant), like the python/systems references.
5. Mirror the change to the repo copy and push. Use the `skill-creator` skill to eval triggering.
