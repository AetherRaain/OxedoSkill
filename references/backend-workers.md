# TypeScript + Hono on Cloudflare Workers + D1

Distilled from jeffallan/api-designer + typescript-pro + wshobson JS/TS, scoped to Hono on Workers
with D1/R2/KV (OxedoScans) and similar edge backends.

## Route structure (the OxedoScans pattern)

- One Hono sub-app per domain in `api/src/routes/*.ts`, mounted in `api/src/index.ts` under
  `/api/<name>`. Keep `index.ts` as wiring only (CORS, mounts, `onError`, `notFound`).
- Guard with middleware: `requireAuth` (valid session) and `requireRole(...roles)` (authorization).
  Apply at the sub-app level for whole-section gates: `admin.use('*', requireAuth, requireRole('admin','moderator'))`.
- Every handler: `try/catch` → on error `return c.json({ error, message }, status)`. Let unexpected
  errors bubble to the centralized `app.onError` (structured JSON log + generic client message —
  never leak stack traces to clients).
- Validate input with zod schemas (`api/src/types/index.ts`). Reject with 400 + the validation error.

## D1 (SQLite) patterns

- Parameterize **everything**: `db.prepare('... WHERE id = ?').bind(id)`. Never string-concat user
  input into SQL.
- `.run()` for writes (`meta.changes` tells you rows affected), `.all()` for lists (`.results`),
  `.first()` for a single row/column.
- **Idempotency / no double-charge**: claim a UNIQUE row *before* doing the side effect, then act.
  Example (coin unlock): `INSERT OR IGNORE INTO chapter_unlocks ...` → if `meta.changes === 0`,
  it was already unlocked, so return early without charging. Only charge after a successful claim;
  roll the claim back if the charge fails.
- Multi-statement atomicity: `db.batch([...])` runs in one transaction (used for refresh-token
  rotation: delete old + insert new together).
- Schema lives in `src/db/schema.sql` (+ `seed.sql`). Use `CREATE TABLE IF NOT EXISTS`, add indexes
  on every column you filter/join on. Re-seed with `npm --prefix api run db:init`.

## Auth model

- Access JWT (1h) + rotating refresh token (30d), both HttpOnly cookies via `hono/cookie`.
- Refresh is **single-use**: on `/refresh`, verify, then `batch([delete old, insert new])` and set
  new cookies. The old refresh token must stop working (tested).
- Passwords: PBKDF2-HMAC-SHA256 via Web Crypto (`utils/crypto.ts`), format
  `pbkdf2:<iters>:<saltHex>:<hashHex>`; constant-time compare.
- `requireAuth` re-checks ban/suspension in D1 each request (correct for security; cache via short
  KV TTL or a token-version claim only if it becomes a measured hotspot).

## Workers best practices

- **No global mutable state** for per-request data — Workers reuse isolates across requests. Keep
  request state in the Hono context (`c.set/get`), not module-level vars.
- **Don't leave floating promises** — `await` writes (KV/D1) you depend on; use `c.executionCtx
  .waitUntil(p)` for fire-and-forget work that should outlive the response.
- Secrets via `wrangler secret put` (prod), never in `vars`/committed config. `JWT_SECRET` must be a
  real secret in production (the dev value in `wrangler.dev.jsonc` is for local only).
- Rate-limit abuse-prone routes with the KV limiter (`middleware/rateLimit.ts`) — fails open, returns
  429 + `Retry-After`. Login 15/min, register 5/10min, comment 10/min, chat 20/min, report 10/10min.
- Bindings (DB/BUCKET/KV/AI) are typed in `types.ts` `Bindings`. Add new bindings there + in
  `wrangler.jsonc` (and the dev config).

## API design

- Consistent JSON envelope: success `{ success: true, ...data }`; error `{ error, message }` + HTTP
  status. Use real status codes (400 validation, 401 unauth, 402 payment, 403 forbidden, 404, 409
  conflict, 429, 500).
- Paginate/limit list endpoints (`LIMIT`, cursor or offset). Don't return unbounded result sets.
- Audit sensitive admin actions to `audit_logs`. Emit user-facing events via `utils/notify.ts`.
