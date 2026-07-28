# CLAUDE.md

Guidance for Claude Code (and any agent) editing this repository.

This repo holds two single-file HTML keynote decks for a Stanford GSB
Chapter Singapore talk: **Enterprise AI in Practice #2 — Governance as
Advantage**. Both files are entirely **JavaScript-array-driven** — there
is no hand-written `<section class="slide">` markup here, and none of the
"one `<section>` at a time" editing rules from decks like the original
Session 1 keynote (`index.html` in `geledek/gsb-keynote-2026`) apply. Do
not go looking for that pattern in this repo; it isn't here.

---

## How the slide system works

Both `session2-intro.html` and `shadow-ai.html` share the same
architecture, near the top and bottom of their single `<script>` block:

1. **`const S = []`** — an array of plain slide-description objects, each
   with a `type` (e.g. `'cover'`, `'toc'`, `'case'`, `'riskread'`,
   `'options'`, `'call'`) and whatever fields that type needs. Slides are
   added with `S.push({...})`, in on-screen order.
2. **`render(s, i)`** — dispatches on `s.type`, builds that slide's DOM via
   a template-literal `innerHTML` string, and returns the `<section>`
   element. `em()` is a small helper that turns `*word*` into an italic
   accent span.
3. **Mount** — `S.forEach(...)` renders every slide once at load, appends
   it to `#stage`, and builds a matching dot (`#dots`) and index-overlay
   entry (`#idxGrid`) for it.
4. **`go(i)`** — the only navigation primitive. Toggles `.active` on the
   target slide and its dot, updates the footer text, and (in
   `session2-intro.html` only) dispatches that slide's ported animation
   recipe — see below.
5. **Keyboard** — `ArrowRight` / `Space` advance, `ArrowLeft` goes back,
   `Escape` toggles the full-deck index overlay (click any entry to jump).

**To add, remove, or reorder a slide: edit the `S` array** (and add a
`render()` branch + CSS if it's a new slide type). Don't hand-build
`<section>` elements — the mount loop already does that from `S`.

Page numbering is automatic (`NN of TOTAL` in `go()`), not hand-maintained
text in each slide — there is no footer-renumbering step to remember here.

---

## `session2-intro.html` specifics

Ten slides: a cover, a Table of Contents (reused twice — first appearance
highlights "Recap," the final appearance highlights "The Shadow AI Role
Play" and carries the `Continue →` link into `shadow-ai.html`), and seven
slides **restored verbatim** from the original Session 1 keynote
(`index.html`, in `geledek/gsb-keynote-2026`):

| `data-layout` | Recipe | What it is |
|---|---|---|
| `SWISS-SURVEY` | `pillar-fade` | "What You Told Us" |
| `SWISS-HOOK-95` | `hook-95` | "The GenAI Divide" (95%) |
| `SWISS-JCURVE-FRESH` | `jcurve-fresh` | "The Productivity J-Curve" |
| `SWISS-ICEBERG-IMG` | `iceberg-img` | "Hardest Challenges" (77%) |
| `SWISS-THREE-LAYERS` | `layers-stack` | "Enterprise AI Success" (70/20/10) |
| `CHANGE-ARC-2` | `pillar-fade` | "Change Has a Lifecycle" |
| `SWISS-ACCELERATE-3` | `pillar-fade` | "Crossing the Valley of Death" |

These seven are rendered through a dedicated `'indexport'` slide type: the
verbatim `<section>...</section>` HTML (unchanged from the original) is
parsed via a raw string and mounted directly, tagged with an
`.index-port` class.

**Why `.index-port` exists:** the verbatim markup references
`var(--paper)`, `var(--ink)`, `var(--sans)`, `var(--mono)`, etc., using
the *original* deck's color/type values — which are different from this
deck's own `--paper` / `--ink` / `--sans` / `--mono`. The `.slide.index-port`
CSS rule re-declares those custom properties with the original values,
scoped only to these seven slides, so they render correctly without
touching (or colliding with) this deck's own theme variables used by every
other slide type. If you need to adjust one of these seven slides
visually, work within `.index-port`'s scope, not the deck's root `:root`.

**Animation recipes:** `ipHook95`, `ipJCurveFresh`, `ipIcebergImg`,
`ipPillarFade`, and `ipLayersStack` are direct ports of index.html's
`rHook95` / `rJCurveFresh` / `rIcebergImg` / `rPillarFade` / `rLayersStack`
functions, dispatched from `go()` via the `IP_RECIPES` map keyed on
`data-animate`. Four of the five use `ipMotionAnimate()`, which calls
`window.__ipMotionFn` (Motion One's `animate`, loaded from
`assets/motion.min.js` by a `<script type="module">` at the bottom of the
file) if it's loaded yet, or snaps straight to the final state if not —
so a fast keypress right after page load never breaks. `layers-stack`
doesn't touch Motion One at all; it's a manual click-to-reveal gated by
`window.__pipeAdvance`, same pattern as the original.

`images/04-iceberg-bg.png` is the background photo for the iceberg slide
— don't remove it without also removing/replacing that slide.

---

## `shadow-ai.html` specifics

Twenty-four slides: cover, scenario/roles, four case studies overview,
"what's at stake," the risk-tier method (tiers + grid), how the evening
runs, then four cases — each case is **the case → risk read → options
(+ room vote) → recommendation**, added via the `addCase(n, name,
tierLabel, {...})` helper — and a close. No index.html port here, no
Motion One dependency, no external images.

---

## Deployment

No GitHub Actions workflow and no `VERCEL_TOKEN` secret live in this repo
on purpose — deploys are handled by Vercel's native GitHub integration
once the project is connected through the Vercel dashboard (a manual step
on Arman's end). Don't add a CI deploy workflow or a Vercel token secret
unless that changes.

| Branch | Purpose |
|---|---|
| `main` | Production. Protected — no direct pushes, 1 approval required. |
| `preview` | Where work happens. Every push gets its own preview URL once Vercel is connected. |

- Never push directly to `main` — always PR from `preview`.
- Test on the staging URL before requesting review.

## Navigation (in browser)

| Key | Action |
|---|---|
| `→` / `←` | Next / previous slide |
| `Space` or click | Advance |
| `ESC` | Slide index |
