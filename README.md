# GSB EAI Governance 2026

**Enterprise AI in Practice #2: Governance as Advantage** — Session 2 of the
Enterprise AI in Practice series, SQ Collective × Gen-AI Labs, Singapore.

**Live deck:** _pending — Vercel project not yet connected (see Deployment below)_

Migrated from the `preview` branch of
[geledek/gsb-keynote-2026](https://github.com/geledek/gsb-keynote-2026),
where this content was originally built alongside the Session 1 keynote.
That repo and its PR #10 are untouched — this is a parallel copy, not a
replacement.

---

## What's here

Two single-file HTML decks, each a self-contained `<script>`-driven slide
system (see `CLAUDE.md` for how they work and how to edit them):

- **`session2-intro.html`** — the evening's intro: welcome, table of
  contents, and a seven-slide recap restored verbatim from the Session 1
  keynote (`index.html` in the original repo). Ends on a "Continue →" link
  into `shadow-ai.html`.
- **`shadow-ai.html`** — the Shadow AI role-play: four cases, each worked
  through the same shape (the case, a risk read, options + a room vote, a
  recommendation).

Supporting files:
- **`images/04-iceberg-bg.png`** — background photo for the "Hardest
  Challenges" slide in `session2-intro.html`.
- **`assets/motion.min.js`** — the Motion One animation library, used by
  the ported recap slides' entrance animations.
- **`vercel.json`** — rewrites `/` to `/session2-intro.html`, so the deck
  serves at the project's root URL.

---

## Making changes

```bash
git checkout preview
# edit session2-intro.html or shadow-ai.html
git add session2-intro.html
git commit -m "slide: what you changed"
git push
```

This auto-deploys to a staging URL once the Vercel project is connected
(see Deployment). Share it for review before going live.

When ready to publish → open a Pull Request from `preview` to `main`.

---

## Rules

- **Never push directly to `main`** — always go through a PR
- One approval required before merging
- Test on the staging URL before requesting review

---

## Deployment

This repo intentionally has **no GitHub Actions deploy workflow and no
`VERCEL_TOKEN` secret**. Deploys are handled by Vercel's native GitHub
integration once the repo is connected through the Vercel dashboard — a
manual, one-time step. After that, every push to `preview` gets its own
preview URL, and merges to `main` deploy to production, the same as any
other Vercel-connected repo.

---

## Navigation (in browser)

| Key | Action |
|---|---|
| `→` / `←` | Next / previous slide |
| `Space` or click | Advance |
| `ESC` | Slide index |

<!-- deploy-trigger: force a Vercel preview deployment for the initial project connection -->
