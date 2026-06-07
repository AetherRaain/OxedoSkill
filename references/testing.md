# Testing — TDD + the OxedoScans harness

Distilled from superpowers (TDD) + jeffallan/test-master, adapted to this stack.

## TDD — the iron law

**NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST.** Red → Green → Refactor:

1. **RED** — write the smallest test that captures the next behavior. Run it. **Watch it fail for
   the right reason.** If it didn't fail, or failed for the wrong reason, the test is wrong.
2. **GREEN** — write the minimal code to make it pass. No extra features.
3. **REFACTOR** — clean up while staying green. Re-run after each change.

If you wrote implementation before the test: delete it, write the test, reimplement from the test.
Don't keep the old code as "reference". Exceptions (prototype/generated/config) → ask the human.

**Test behavior, not implementation.** Assert on observable outcomes (response status, DB state,
returned values), not internal call order. A test that breaks on every refactor is testing the
wrong thing.

## Where bugs cost the most — test these first

Prioritize money + auth + data-integrity paths over coverage percentage:
- Refresh-token rotation (single-use; old token rejected after rotation).
- Coin/credit spends are idempotent (no double-charge on retry/double-click).
- Insufficient-balance rejects cleanly and leaves no partial state.
- Auth: login/logout/me, banned/suspended blocking, role gating on protected routes.
- Payment webhooks (when added): idempotent on duplicate delivery; signature verified.

## The OxedoScans test harness (`api/test/`)

The Cloudflare workers test pool can't resolve modules under a path with spaces, so the API is
tested in **plain Node + Vitest** with an in-memory **D1 shim over `node:sqlite`**:

- `api/test/harness.ts` — `createEnv()` builds a fresh `:memory:` DB seeded from the real
  `src/db/schema.sql`, plus a KV shim, and exposes `call/post/cookiesFrom/seedUser/login/...`
  helpers that drive the **real Hono app** via `app.request(path, init, env)`.
- `node:sqlite` is loaded via `createRequire` (Vite doesn't recognize the new builtin).
- Each test does `beforeEach(() => { env = createEnv() })` for full isolation.
- Run: `npm --prefix api test` (or `test:watch`).

Pattern for a new API test:
```ts
import { describe, it, expect, beforeEach } from 'vitest';
import { createEnv, post, login, seedUser, type TestEnv } from './harness';

describe('feature', () => {
  let env: TestEnv;
  beforeEach(() => { env = createEnv(); });
  it('does the thing', async () => {
    await seedUser(env, { username: 'u', role: 'user', coins: 100 });
    const cookies = await login(env, 'u');
    const res = await post(env, '/api/...', { /* body */ }, cookies);
    expect(res.status).toBe(200);
  });
});
```

Extend the harness with new `seedX` helpers rather than inlining raw SQL in every test.

## Frontend tests

- Component tests: React Testing Library — assert on rendered output and user interactions, not
  internal state. Query by role/label (accessibility-first).
- E2E (when added): Playwright for the critical flows — reader open, login, unlock/checkout.
- Always `tsc`/build before claiming a UI change is done; verify in the preview when observable.
