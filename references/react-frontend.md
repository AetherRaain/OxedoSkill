# React + Vite + RTL frontend

Distilled from jeffallan/react-expert + typescript-pro, scoped to React 19 + Vite + react-router v7
+ i18next (OxedoScans) and any similar React/TS app.

## Workflow for a component or feature

1. **Analyze** — component hierarchy, state needs, data flow. Where does data come from?
2. **Choose patterns** — local state vs context vs store; data fetching approach.
3. **Implement** — typed components, props, and handlers.
4. **Validate types** — `tsc --noEmit`; fix every error before moving on.
5. **Optimize** — memoize only where a real render cost exists; ensure accessibility (roles/labels).
6. **Test** — RTL for behavior; verify in preview when observable.

## State & data

- **Local first**: `useState`/`useReducer` for component-local state. Lift only when shared.
- **Context** for cross-cutting, low-churn state (auth, theme, language, toasts) — OxedoScans uses
  `contexts/{Auth,Theme,Toast}Context`. Don't put high-frequency state in context (causes wide
  re-renders); use a store (Zustand) or local state.
- **Data fetching**: keep it in one layer. In OxedoScans, **all** calls go through
  `frontend/src/utils/api.js` (`seriesApi`, `usersApi`, `adminApi`, `coinsApi`, ...). Add new
  endpoints to the right `*Api` object. The client auto-refreshes once on 401 and retries.
- Handle the three states explicitly: loading (spinner/skeleton), error (toast/empty), data.

## Performance

- Don't pre-optimize. Measure first. Then: `React.memo` for expensive pure children, `useMemo`/
  `useCallback` for referentially-stable props passed to memoized children or effect deps.
- Code-split routes with `React.lazy` + `Suspense` (OxedoScans already does this in `App.jsx`).
- Lists: stable `key`s (never index when items reorder). Virtualize long lists.
- Images: lazy-load below the fold, preload the next page in the reader, prefer WebP/AVIF.

## RTL/LTR (critical for OxedoScans)

- Use **logical CSS properties**: `margin-inline`, `padding-inline`, `inset-inline-start/end`,
  `text-align: start/end`. Never hardcode `left`/`right` for directional layout.
- Direction-specific tweaks go under `[dir="rtl"] ...` overrides.
- All user-facing strings are bilingual: `isAr ? 'عربي' : 'English'` or i18next keys. No hardcoded
  English in UI. No emojis — use Lucide icons.
- Series titles always render English (`title_en || title_ar`) regardless of UI language.

## TypeScript discipline (for .tsx / typed projects)

- Type-first: define the data shapes, then build to them. Explicit return types on exported funcs.
- Prefer discriminated unions over boolean flags for variant state (`{status:'loading'} | ...`).
- Use type guards and `unknown` over `any`. Branded types for IDs where mix-ups are costly.
- `tsc --noEmit` is a gate, not a suggestion — zero errors before "done".

## Theming

- Dark "Midnight Ink" tokens + light overrides via `[data-theme]`; persisted, no-flash bootstrap.
- Reader keeps a warm `.paper-surface`. Reuse existing CSS tokens (`--bg-*`, `--accent*`,
  `--text-*`, `--glass`, `--border*`, `--r-*`) — don't invent one-off colors.
