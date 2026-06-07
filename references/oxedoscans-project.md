# OxedoScans — operations & conventions

Arabic-first (RTL) + English (LTR) digital comics platform (manga/manhwa/manhua).
Monorepo: `frontend/` (React 19 + Vite + react-router v7 + i18next + Framer Motion) and
`api/` (Hono on Cloudflare Workers: D1 + R2 + KV + Workers AI). One Worker serves SPA + `/api/*`.

## Run / build / test

```bash
npm run install:all              # install frontend + api deps
npm --prefix api run db:init     # create + seed LOCAL D1 (schema.sql + seed.sql)
npm run dev                      # vite :5173 + wrangler :8787 (vite proxies /api -> 8787)
npm --prefix api test            # Vitest (node:sqlite D1 shim) — money/auth path tests
npm --prefix frontend run build  # production build -> frontend/dist
```

Demo accounts (password `password123`): `admin`, `moderator`, `solo_translator`,
`translator_team`, `premium_reader`, `normal_reader`.

API alone (no AI binding, for previews): `npx wrangler dev -c api/wrangler.dev.jsonc --port 8787`.

## Known gotchas

- **wrangler reload hang**: editing `api/src/routes/*.ts` can wedge `wrangler dev` — port 8787
  goes dead, last log "Reloading local server...". Fix: kill the workerd/8787 process and restart.
- **Vite HMR noise**: editing `App.jsx` triggers benign "Failed to reload" Fast-Refresh warnings;
  a dev-server restart clears them. Production build is unaffected.
- **Vitest + path with spaces**: `@cloudflare/vitest-pool-workers` cannot resolve modules under a
  path containing spaces (this repo lives under "My Projects"). Tests therefore run in plain Node
  with a `node:sqlite` D1 shim (`api/test/harness.ts`), not the workers pool. `node:sqlite` is
  loaded via `createRequire` so Vite's transformer never sees the unrecognized builtin.

## Architecture

| Layer | Tech |
|---|---|
| Frontend | React 19, Vite, react-router v7, i18next, Framer Motion. Code-split routes, dark/light, RTL/LTR |
| API | Hono on Cloudflare Workers — one Worker serves SPA (`assets`) + `/api/*` |
| DB | Cloudflare D1 (SQLite) — `api/src/db/schema.sql` + `seed.sql` |
| Files | Cloudflare R2 (page images, proxied via `/api/pages/proxy/:id` for anti-theft) |
| Cache/limits | Cloudflare KV (rate limits, translate cooldowns) |
| AI | Workers AI (`@cf/meta/m2m100-1.2b` auto-translate, premium-gated) |
| Auth | JWT access (1h) + rotating refresh (30d), HttpOnly cookies; PBKDF2 passwords |

## Roles & dashboards

DB `users.role`: `guest | user | premium | translator | team_member | moderator | admin`.
UI labels via `frontend/src/utils/roles.js`: Solo Translator (`translator`), Translation Team
(`team_member`). Hierarchy: admin > moderator > translator/team.

- **Admin** → `/admin` (all tabs incl. analytics, monetization, import, settings, logs, roadmap).
- **Moderator** → `/admin` (focused subset; no settings/import/logs/roadmap/role changes).
- **Translator / Team** → `/studio` (upload, submissions, projects, glossary; team adds My Team).

Frontend gates tabs by role; backend enforces via `requireRole(...)`. Both layers must agree.

## Conventions (enforced)

- All frontend API calls go through `frontend/src/utils/api.js` (grouped `*Api` objects). Add new
  endpoints there, never inline `fetch`.
- Auth = HttpOnly cookies (access + rotating refresh) set via `hono/cookie`. `api.js` auto-refreshes
  once on 401 and retries.
- RTL/LTR via logical CSS props (`margin-inline`, `inset-inline-*`) + `[dir="rtl"]` overrides.
  Lang/theme/RTL all live in context + i18next.
- Series titles always display English (`title_en || title_ar`) regardless of UI language (searchable).
- Upload → moderation → publish pipeline; series/chapters start pending and need approval.
- Design = "Midnight Ink" dark cinematic theme; reader keeps a warm `.paper-surface`. No emojis —
  Lucide icons only. Fonts: Anton/Rakkas (display), Sora/Tajawal (UI), Newsreader/Markazi (reader).

## Adding common things (the established patterns)

- **New admin tab**: add a route handler in `api/src/routes/admin.ts` (guard with `requireRole`),
  add the call to `adminApi` in `api/api.js`, build a `components/admin/XPanel.jsx`, then register
  it in `pages/Admin.jsx` (import + `ALL_TABS` entry with `roles` + render line). Update
  `components/admin/RoadmapPanel.jsx` ROADMAP status.
- **New DB table/column**: edit `api/src/db/schema.sql` (use `IF NOT EXISTS`) and `seed.sql`; a fresh
  `npm --prefix api run db:init` picks it up. Add indexes for query columns.
- **Money/auth path change**: add/extend a test in `api/test/` first (TDD). Keep coin spends
  idempotent (claim the row before charging — see `routes/coins.ts` unlock).
- **Notifications**: emit via `api/src/utils/notify.ts` (`notify`, `notifyBookmarkers`).

## Status & priorities (read PLAN.md for the live version)

Tier 0 (blocks money, user-only decisions): content/legal model; real payments processor
(Stripe vs Paddle) + business entity. Tier 1: real image pipeline (R2 + WebP + preload),
fast mobile reader. Tier 2 (mostly done): rate limiting, Turnstile, structured logging,
critical tests. Done milestones: M1 translation teams, M4 content versioning, M5 notifications +
user moderation, M6 analytics. Staying local — not deployed yet.
