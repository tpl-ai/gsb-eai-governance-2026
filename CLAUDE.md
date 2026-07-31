# CLAUDE.md

Guidance for Claude Code (and any agent) editing this repository.

This repo holds the deck for **Enterprise AI in Practice #2 — Governance
as Advantage** (Stanford GSB Chapter Singapore). It is **two files**:

- **`index.html`** — a thin outer shell. Its only jobs are: hold a fixed
  1280×720 `<iframe>` pointed at `deck.html`, scale that iframe to fit
  whatever real screen it's on, and show a one-time portrait-orientation
  hint on narrow phones. It has no slide content of its own.
- **`deck.html`** — the actual 30-slide deck: the `S`-array-driven slide
  system, all CSS, all navigation (keyboard, touch, dots, index overlay).

Both are entirely **JavaScript-array-driven** — there is no hand-written
`<section class="slide">` markup here, and none of the "one `<section>` at
a time" editing rules from decks like the original Session 1 keynote
(`index.html` in `geledek/gsb-keynote-2026`) apply. Do not go looking for
that pattern in this repo; it isn't here.

---

## Why the shell/iframe split exists (mobile scaling)

Most of this deck's type scale uses `vw`/`vh` (`min(5vw,8.4vh)` titles,
etc.) so slides hold desktop proportions across screen sizes. But `vw`/`vh`
always resolve against the **real device viewport** — there is no way to
make them resolve against a fixed virtual canvas with CSS alone. A
`transform:scale()` wrapper only rescales pixels *after* layout; it doesn't
change what `vw`/`vh` compute against, so on a narrow phone a `5vw` title
still computes tiny, then gets shrunk *again* by the transform — worse than
doing nothing.

An `<iframe>` genuinely solves this: a nested browsing context's internal
viewport **is** the iframe element's own rendered CSS box, full stop,
regardless of the outer document's real size. So `index.html` gives the
iframe a fixed `width:1280px;height:720px`, and everything inside
`deck.html` — including the seven ported June slides, which must not be
edited — correctly treats that as a genuine 1280×720 viewport. `index.html`
then just scales the iframe *element* to fit the real screen via
`transform:scale(min(vw/1280, vh/720))`, recomputed on resize and
orientationchange. Nothing inside `deck.html` needed to change to make
this work — that's the point of the split.

If you're touching mobile behavior, the split is:
- **Scaling, portrait hint, pinch-zoom exception** → `index.html`.
- **Slide content, navigation, touch gestures, everything else** →
  `deck.html`.

### Cross-frame plumbing (small, on purpose)

- `deck.html`'s `go(i)` calls `window.parent.__onDeckSlideChange(i)` if
  embedded (guarded by `window.parent !== window`), so `index.html` can
  toggle its own `<meta name="viewport">` — pinch-zoom is disabled deck-wide
  except on slide index `14` (`riskimage`, the dense Lewis Ho graphic),
  where it's re-enabled. If you reorder slides, update
  `RISK_IMAGE_SLIDE_INDEX` in `index.html`.
- `index.html` calls `frame.contentWindow.focus()` on load (so a
  keyboard-driven presenter clicker works immediately, no initial tap
  needed) and forwards `keydown` from itself into the iframe's document as
  a fallback if focus ever lands outside the iframe. When the iframe
  already has focus (the common case), that fallback never fires — the
  event goes straight to `deck.html`'s own listener, no double-handling.
- Both of these only work because `go`/`toggleIndex` are `function`
  declarations, not `const`/`let` — top-level `function` statements in a
  classic (non-module) script attach to `window` and are reachable as
  `iframe.contentWindow.go(...)` from outside; `let current = 0` does not
  attach to `window` and is intentionally not relied on cross-frame.

---

## How the slide system works (`deck.html`)

1. **`const S = []`** — an array of plain slide-description objects, each
   with a `type` (e.g. `'cover-intro'`, `'toc'`, `'case'`, `'riskread'`,
   `'options'`) and whatever fields that type needs, plus a `tone`
   (`'light'` / `'accent'` / `'dark'`) that `render()` reads to pick the
   slide's background. Slides are added with `S.push({...})`, in
   on-screen order. **Every non-`indexport` push needs an explicit `tone`
   field** — there is no type-based fallback anymore; a missing `tone`
   silently renders `'light'`, which is how a same-tone run slipped in
   undetected once already (see git history on this file).
2. **`render(s, i)`** — dispatches on `s.type`, builds that slide's DOM via
   a template-literal `innerHTML` string, and returns the `<section>`
   element. `em()` turns `*word*` into `color:var(--accent);font-weight:600`
   — there is no serif/italic emphasis anywhere in this deck.
3. **Mount** — `S.forEach(...)` renders every slide once at load, appends
   it to `#stage`, and builds a matching dot (`#dots`) and index-overlay
   entry (`#idxGrid`) for it.
4. **`go(i)`** — the only navigation primitive. Toggles `.active` on the
   target slide and its dot, updates the footer text, dispatches that
   slide's ported animation recipe if it has one, dispatches the native
   options-slide two-click reveal if it has one, and notifies the parent
   shell frame (see above).
5. **Navigation** — keyboard (`ArrowRight`/`Space` advance, `ArrowLeft`
   back, `Escape` toggles the index overlay) and touch (tap right
   two-thirds to advance, tap left third to go back, swipe left/right,
   ~50px threshold, ignored when vertical movement dominates). Both paths
   funnel through `go()` and check `window.__pipeAdvance` before advancing,
   so staged slides (see below) advance their stage first either way. A
   small `#indexToggleBtn` (bottom-right) opens the index overlay by tap,
   since `Escape` isn't reachable on a touch-only device.

**To add, remove, or reorder a slide: edit the `S` array** (and add a
`render()` branch + CSS if it's a new slide type). Don't hand-build
`<section>` elements — the mount loop already does that from `S`. Check the
tone sequence afterward — no 3+ same-tone run — since the untouchable
ported slides constrain which slides around them can carry which tone.

Page numbering is automatic (`NN of TOTAL` in `go()`), not hand-maintained
text in each slide — there is no footer-renumbering step to remember here.

---

## Staged slides (`window.__pipeAdvance`)

Two slide types reveal in more than one step on the same slide, both using
the same mechanism: `go()` sets `window.__pipeAdvance = null` on every
navigation, then a per-slide setup function may replace it with a stepper
`function(){ ...; return true|false; }`. The keyboard and touch handlers
both check `if(window.__pipeAdvance && window.__pipeAdvance()) return;`
before calling `go(current+1)` — if the stepper returns `true`, the
slide's own click/tap was "consumed" advancing an internal stage instead
of moving to the next slide; once the stepper returns `false` (all stages
done), it sets itself back to `null` and normal advance resumes.

- **`ipLayersStack`** (ported, index-port scope, `SWISS-THREE-LAYERS` /
  "Enterprise AI Success") — 5 stages, do not touch.
- **`optionsReveal`** (native, every `options` slide) — 2 stages: neutral
  cards, then the recommended card (`el.dataset.recLetter`) turns
  `.recommended` and the `.options-reasoning` line fades in.

If you add a new staged slide type, follow this exact pattern (a setup
function called from `go()`, reset state at the top so replaying the slide
works, `window.__pipeAdvance` as the sole gate) — don't invent a second
staging mechanism.

---

## Ported June slides — do not restyle

Seven slides are restored **verbatim** from the original Session 1 keynote
(`index.html`, in `geledek/gsb-keynote-2026`), rendered through a dedicated
`'indexport'` slide type: the raw `<section>...</section>` HTML (unchanged
from the original, aside from stripping the hard-coded June page-number
footers — see below) is parsed from a string and mounted directly, tagged
with an `.index-port` class.

| `data-layout` | Recipe | What it is |
|---|---|---|
| `SWISS-SURVEY` | `pillar-fade` | "What You Told Us" |
| `SWISS-HOOK-95` | `hook-95` | "The GenAI Divide" (95%) |
| `SWISS-JCURVE-FRESH` | `jcurve-fresh` | "The Productivity J-Curve" |
| `SWISS-ICEBERG-IMG` | `iceberg-img` | "Hardest Challenges" (77%) |
| `SWISS-THREE-LAYERS` | `layers-stack` | "Enterprise AI Success" (70/20/10) |
| `CHANGE-ARC-2` | `pillar-fade` | "Change Has a Lifecycle" |
| `SWISS-ACCELERATE-3` | `pillar-fade` | "Crossing the Valley of Death" |

**Why `.index-port` exists:** the verbatim markup references
`var(--paper)`, `var(--ink)`, `var(--sans)`, `var(--mono)`, etc., using the
*original* deck's color/type values, which happen to now be identical to
this deck's own tokens (both ultimately trace to the same June design
system) but are still declared as a separate scope — the `.slide.index-port`
rule re-declares those custom properties, scoped only to these seven
slides, so nothing here depends on this deck's global `:root` staying in
sync with them. If you touch the global `:root` tokens, verify these seven
still render correctly rather than assuming the scoping makes them immune.

Their hard-coded June footers (`03 OF 23` … `20 OF 23`) were stripped —
this deck numbers itself out of its own total (30, not 23) via `go()`; the
source attribution beside each stripped number (where one existed) was
kept.

**Animation recipes:** `ipHook95`, `ipJCurveFresh`, `ipIcebergImg`,
`ipPillarFade`, and `ipLayersStack` are direct ports of index.html's
`rHook95` / `rJCurveFresh` / `rIcebergImg` / `rPillarFade` / `rLayersStack`
functions, dispatched from `go()` via the `IP_RECIPES` map keyed on
`data-animate`. Four of the five use `ipMotionAnimate()`, which calls
`window.__ipMotionFn` (Motion One's `animate`, loaded from
`assets/motion.min.js` by a `<script type="module">` at the bottom of the
file) if it's loaded yet, or snaps straight to the final state if not — so
a fast keypress/tap right after page load never breaks. `layers-stack`
doesn't touch Motion One at all; see "Staged slides" above.

`images/04-iceberg-bg.png` is the background photo for the iceberg slide —
don't remove it without also removing/replacing that slide.
`images/lewis-risk-tiers.png` is Lewis Ho's published risk-framework
graphic (native slide, not ported) — it uses its own green/blue/orange/red
tier coding, a deliberate exception to this deck's cardinal-only palette;
leave its colors alone, they're his IP shown unaltered.

---

## The rest of the deck (native slides)

Cover → Table of Contents (reused three times as a section marker —
Recap, Role Play, Skills Demo — each highlighting the current item, no
"Continue" link since everything lives in one file) → the seven ported
slides → TOC again → Scenario → Four Case Studies (titles only, the
descriptions live on each case's opener slide) → What's at Stake → How
Tonight Runs (flow) → the risk-tiers image → the placement matrix → four
cases (each **case opener → risk read → options**, added via the
`addCase(n, name, {...})` helper — no separate recommendation slide; the
call is the options slide's second stage) → Close → TOC a third time.

---

## Deployment

No GitHub Actions workflow and no `VERCEL_TOKEN` secret live in this repo
on purpose — deploys are handled by Vercel's native GitHub integration
once the project is connected through the Vercel dashboard (a manual step
on Arman's end). Don't add a CI deploy workflow or a Vercel token secret
unless that changes. `vercel.json` is intentionally empty (`{}`) — no
rewrite is needed; `index.html` (the shell) serves at root by default, and
`deck.html` is just a sibling static asset the shell's iframe loads by
relative path.

| Branch | Purpose |
|---|---|
| `main` | Production. Protected — no direct pushes, 1 approval required. |
| `preview` | Where work happens. Every push gets its own preview URL once Vercel is connected. |

- Never push directly to `main` — always PR from `preview`.
- Test on the staging URL before requesting review — on a real phone if
  the change touches scaling, touch nav, or the portrait hint; jsdom
  cannot validate an iframe's actual rendered viewport size since it does
  no real CSS layout.

## Navigation

| Input | Action |
|---|---|
| `→` / `Space` | Next slide (or advance a staged slide's next stage) |
| `←` | Previous slide |
| `Esc` | Toggle the full-deck index overlay |
| Tap right two-thirds | Next slide (same staging-aware path as `→`) |
| Tap left third | Previous slide |
| Swipe left / right | Next / previous (~50px threshold) |
| Tap `⊞` (bottom-right) | Toggle the index overlay (touch equivalent of `Esc`) |
