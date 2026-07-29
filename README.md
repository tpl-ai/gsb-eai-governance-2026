# GSB EAI Governance 2026

**Enterprise AI in Practice #2: Governance as Advantage** — Session 2 of the
Enterprise AI in Practice series, SQ Collective × Gen-AI Labs, Singapore.

**Live deck:** https://gsb-eai-governance-2026.vercel.app

Migrated from the `preview` branch of
[geledek/gsb-keynote-2026](https://github.com/geledek/gsb-keynote-2026),
where this content was originally built alongside the Session 1 keynote.
That repo and its PR #10 are untouched — this is a parallel copy, not a
replacement.

---

## What's here

A single 30-slide deck, split across two files (see `CLAUDE.md` for the
full slide-system and mobile-scaling architecture):

- **`index.html`** — a thin shell. Holds a fixed 1280×720 iframe pointed
  at `deck.html`, scales it to fit the real screen (desktop, tablet,
  phone, portrait or landscape), and shows a one-time "rotate for a
  larger view" hint on narrow portrait screens. No slide content lives
  here.
- **`deck.html`** — the actual deck: welcome → three-part table of
  contents (reused as a section marker between Recap / the Shadow AI Role
  Play / Skills Demo) → seven slides restored verbatim from the Session 1
  keynote → the Shadow AI role-play (four cases, each worked through the
  same shape: the case, a risk read, and options that resolve into a
  recommendation) → close.

Supporting files:
- **`images/04-iceberg-bg.png`** — background photo for the "Hardest
  Challenges" ported slide.
- **`images/lewis-risk-tiers.png`** — Lewis Ho's published four-tier AI
  risk framework graphic, shown full-bleed in place of a built tier
  ladder.
- **`assets/motion.min.js`** — the Motion One animation library, used by
  four of the five ported slides' entrance animations.
- **`vercel.json`** — intentionally empty (`{}`). No rewrite needed:
  `index.html` serves at root by default, and `deck.html` is just a
  sibling static asset the shell's iframe loads by relative path.

---

## Making changes

```bash
git checkout preview
# edit deck.html for slide content, or index.html for the mobile shell
git add deck.html   # or index.html
git commit -m "slide: what you changed"
git push
```

This auto-deploys to a staging URL. Share it for review before going live.
**Test on a real phone** (not just a resized desktop browser window) if
the change touches scaling, touch navigation, or the portrait hint — an
iframe's actual rendered viewport can't be verified by static tools, only
by a real browser.

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
integration — every push to `preview` gets its own preview URL, and
merges to `main` deploy to production, the same as any other
Vercel-connected repo.

---

## Navigation

| Input | Action |
|---|---|
| `→` / `Space` | Next slide |
| `←` | Previous slide |
| `Esc` | Toggle the full-deck index overlay |
| Tap right two-thirds / swipe left | Next slide |
| Tap left third / swipe right | Previous slide |
| Tap `⊞` (bottom-right) | Toggle the index overlay (touch equivalent of `Esc`) |
