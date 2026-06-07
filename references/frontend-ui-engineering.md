# Frontend UI engineering — production-quality UI

Distilled from **addyosmani/agent-skills → frontend-ui-engineering** (Addy Osmani). The goal: UI that
looks built by a design-aware engineer at a top company — **not** generated "AI aesthetic". Pairs with
`react-frontend.md` (the OxedoScans-specific React/RTL rules) and `web-perf` skill for measurement.

## The bar: no generic AI look

- Real design-system adherence (use the existing tokens/spacing/typography — for OxedoScans the
  `--bg-*`/`--accent*`/`--r-*` tokens and Sora/Tajawal fonts). No invented one-off colors/sizes.
- Thoughtful interaction states: hover, focus-visible, active, disabled, loading, empty, error.
- Polished micro-details: consistent spacing rhythm, optical alignment, real shadows/elevation,
  motion with purpose (Framer Motion vocab already in the repo), reduced-motion respected.

## Component architecture

- **Colocate** everything for a component: `Component.tsx`, `Component.test.tsx`, `use-x.ts` hook,
  `types.ts`, styles. One folder per component.
- **Composition over configuration**: expose `<Card><CardHeader/><CardBody/></Card>`, not a `<Card>`
  with 15 props. Composable beats over-configured.
- Keep components focused — one responsibility. Push complex state into a custom hook.
- Container/presentational split where it clarifies: data/logic in hooks, rendering in pure components.

## Accessibility (non-negotiable)

- Semantic HTML first (`button`, `nav`, `main`, `ul/li`) before ARIA. ARIA only to fill gaps.
- Every interactive element keyboard-operable; visible `:focus-visible` ring; logical tab order.
- Label controls (`<label>`, `aria-label`); associate errors with inputs (`aria-describedby`).
- Color contrast ≥ WCAG AA; never rely on color alone to convey meaning.
- Respect `prefers-reduced-motion` and `prefers-color-scheme`. (OxedoScans already themes both.)
- For RTL (Arabic): mirror layout via logical properties, not transforms; test the whole flow in RTL.

## Performance (Core Web Vitals)

- **LCP**: prioritize the hero/largest image; preload it; size images; serve WebP/AVIF; avoid layout
  jank from late-loading fonts (`font-display: swap`, preload key fonts).
- **CLS**: reserve space for images/embeds (width/height or aspect-ratio); no content inserted above
  existing content; avoid layout shift from web fonts.
- **INP/responsiveness**: keep handlers light; debounce/throttle expensive work; avoid large synchronous
  renders; virtualize long lists; code-split routes (repo already uses `React.lazy`).
- Lazy-load below-the-fold images; **preload the next page** in the reader (Tier 1 goal). Measure with
  the `web-perf` skill / Lighthouse — don't guess.

## State & data

- Local state first; lift only when shared; context for low-churn cross-cutting (auth/theme/lang).
- Render the full matrix explicitly: loading (skeleton), empty, error (actionable), success.
- Keep data fetching in one layer — for OxedoScans, `frontend/src/utils/api.js`. No scattered `fetch`.

## Verify (per the iron laws)

`tsc --noEmit` clean, build passes, and **observe it in the browser/preview** before claiming a UI
change done — code reading correctly is not evidence it renders correctly.
