# End-to-end testing

Methodology distilled from **bmad-labs/skills → typescript-e2e-testing** (Given-When-Then, real
infrastructure, test isolation, flaky-test debugging) and adapted with **Playwright** for the React
frontend — which is what OxedoScans actually needs. Complements `testing.md` (unit/API harness + TDD).

## Principles (stack-agnostic)

- **Validate complete workflows from the user's perspective**, against real infrastructure, not mocks.
  E2E proves the seams unit tests can't.
- **Given-When-Then** structure every test:
  - *Given* a known starting state (seeded DB/user/fixtures).
  - *When* the user performs the action (navigate, click, fill, submit).
  - *Then* assert the observable outcome (UI state, response, persisted data).
- **Test isolation**: each test sets up and tears down its own state; no order dependence; unique data
  per test (unique usernames/emails). A test must pass run alone and in the suite.
- **Determinism over flakiness**: wait on conditions/elements, never fixed `sleep`. A flaky test is a
  failing test — debug the race, don't retry-loop it.

## Debugging flaky / failing E2E (root-cause first)

Per the systematic-debugging iron law: read the full error + trace, reproduce, check recent changes.
Common E2E flake causes: timing/races (await the actual condition), shared state between tests,
unseeded/leftover data, network nondeterminism, animation timing. Fix the cause, not the symptom.

## Playwright for the OxedoScans React app

Critical flows to cover (highest user/money value first):
- **Reader open** — series → chapter → pages render (and the premium lock for non-entitled users).
- **Auth** — register/login, session persists, logout.
- **Coins/unlock** — buy pack (mock) → unlock premium chapter → page renders; double-click doesn't
  double-charge (mirrors the API double-spend test at the UI level).
- **Admin** — role-gated routes block non-admins.

Patterns:
- Query by **role/label/text** (accessibility-first): `getByRole('button', { name: ... })`,
  `getByLabel(...)`. Avoid brittle CSS/nth-child selectors.
- Use Playwright **auto-waiting** + web-first assertions (`await expect(locator).toBeVisible()`); no
  manual sleeps.
- Seed state via the API or a test fixture before the test; reset between tests.
- Run headless in CI; use `--ui`/trace viewer locally to debug.
- Keep E2E focused on a handful of critical journeys — they're slower than unit/API tests; let the
  `node:sqlite` API harness (`testing.md`) carry the bulk of backend coverage.

## Verify gate

Run the E2E command fresh and read the result (0 failures) before claiming the flow works. For visual
correctness, capture a screenshot/trace as evidence.
