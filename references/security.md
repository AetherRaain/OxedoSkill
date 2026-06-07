# Security checklist

Distilled from jeffallan/secure-code-guardian + security-reviewer, scoped to a web app with auth +
payments + user content (OxedoScans). Run this lens on every auth/money/user-input change.

## Always

- **Never trust client input.** Validate + normalize server-side (zod). The client amount/price/role
  is a suggestion, not a fact — derive money values server-side (see coin packs defined on the server).
- **Parameterize all SQL.** No string concatenation of user input into queries.
- **AuthN ≠ AuthZ.** `requireAuth` proves identity; `requireRole` proves permission. Gate every
  privileged route with both. Re-check on the server even if the UI already hides the action.
- **Secrets never in code/config/logs.** Use `wrangler secret put`. Rotate the dev `JWT_SECRET`
  before production. Don't log tokens, password hashes, or full request bodies.
- **Cookies**: HttpOnly + SameSite + Secure (in prod). Access token short-lived; refresh rotating &
  single-use. Invalidate refresh tokens on logout, password reset, ban, and suspend.

## Money paths (highest risk)

- **Idempotency**: every credit/debit must be safe to retry. Claim a UNIQUE row before charging;
  treat duplicate as a no-op success. Test the double-spend case.
- **Server-authoritative amounts**: coin pack sizes, prices, unlock costs come from the server, never
  the request body.
- **Payment webhooks** (when added): verify the provider signature; make handlers idempotent on
  duplicate delivery; never grant entitlements from a client call alone — only from a verified webhook.
- Record an immutable ledger (`coin_transactions`) with `balance_after`; reconcile against it.

## User content & abuse

- **Rate-limit** auth, signup, comment, chat, report, and any expensive/AI route (KV limiter, fails
  open, 429 + Retry-After). Signup behind Turnstile (enforced only when keys are set).
- **Sanitize/escape** user content on render; React escapes by default — never `dangerouslySetInnerHTML`
  with user input. Validate URLs (avatar/image) and lengths.
- **Moderation pipeline**: user uploads start pending; publish requires approval. Keep it.
- **Report + audit**: log sensitive admin actions to `audit_logs`; surface user-facing consequences
  via notifications.

## Before production (OxedoScans)

- `wrangler secret put JWT_SECRET` (+ `TURNSTILE_SECRET`, payment secrets when billing lands).
- Remove/disable demo accounts; reseed real data.
- Add external error tracking (Sentry) — currently only structured `console` logs via `onError`.
- Decide the content/legal model (licensed vs scanlation vs translation-platform) before taking money —
  it drives DMCA posture, ToS, and which payment processor will accept you.
- Consider CSRF double-submit token if any cross-site cookie usage is introduced.
