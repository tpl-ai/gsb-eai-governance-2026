# Session 2 Deck — Design Alignment Specification

Target: both decks in one design system, matching the June 2026 Session 1
keynote (`index.html` in `geledek/gsb-keynote-2026`).

Two measurable goals:
1. Average under 30 words per slide (June reference: 36; shadow-ai now: 90)
2. Type sizes matching the June scale (shadow-ai currently runs ~half size)

---

## Part 1 — The Design System

Replace the `:root` block in **both** `session2-intro.html` and
`shadow-ai.html` with these tokens. The shadow-ai deck's current warm-cream
and `#6E1616` red are retired.

```css
:root{
  /* Surfaces */
  --paper:#fafaf8;
  --ink:#0a0a0a;
  --grey-1:#f0f0ee;
  --grey-2:#d4d4d2;
  --grey-3:#737373;

  /* Accent — Stanford Cardinal */
  --accent:#8C1515;
  --accent-on:#ffffff;
  --accent-bright:#C4322D;   /* accent on dark backgrounds only */

  /* Text roles — use these instead of opacity */
  --text-primary:#0a0a0a;
  --text-secondary:#525252;
  --text-helper:#737373;
  --text-on-color:#ffffff;
  --border-subtle:#e0e0e0;

  /* Type */
  --sans:"Inter","Helvetica Neue",Helvetica,Arial,system-ui,sans-serif;
  --mono:"JetBrains Mono","IBM Plex Mono","SF Mono",Consolas,ui-monospace,monospace;
}
```

**Remove entirely:** the `--serif` / Newsreader font and every `.em` italic
serif treatment. The June deck has no serif. Where emphasis is needed, use
`color:var(--accent)` with `font-weight:600`, not italics.

### Type scale

Use dual-constraint sizing (`min(Xvw, Yvh)`) so slides hold up on both wide
and short screens — this is what the June deck does and why its titles stay
large without overflowing.

| Role | Size | Weight | Notes |
|---|---|---|---|
| Display number (95%, 77%) | `min(22vw,32vh)` | 200 | accent color |
| Hero / cover headline | `min(7.2vw,12vh)` | 200 | |
| Slide title | `min(5vw,8.4vh)` | 200 | the workhorse |
| Secondary title | `min(4.6vw,7.6vh)` | 200 | |
| Large item label | `min(3vw,5vh)` | 300 | e.g. list items |
| Body / support text | `max(22px,2vw)` | 400 | |
| Detail text | `max(18px,1.08vw)` | 400 | minimum readable |
| Meta / eyebrow / footer | `14px` | 400-500 | mono, uppercase, `letter-spacing:.16em` |

**Nothing renders below 14px.** Current 10-13px treatments (`.eyebrow`,
`.card-desc`, `.dim-desc`, `.stakes-detail`, `.role-desc`) all move up.

### Layout rules

- Slide title sits top-left, never centered, with the eyebrow above it
- Footer rule: 1px `--border-subtle`, meta text left and right, matching June
- One idea per slide — if a slide carries two arguments, split it
- Content fills the full slide width; no text confined to a narrow column

---

## Part 2 — session2-intro.html

Seven of ten slides are verbatim ports from June and need **no changes**.
Three native slides need alignment.

| # | Slide | Words now | Target | Action |
|---|---|---|---|---|
| 1 | Welcome / cover | 29 | 25 | Restyle to new tokens |
| 2 | Table of Contents | 34 | 20 | Trim item descriptions |
| 3-9 | Ported from June | 15-45 | — | **No change** |
| 10 | TOC + Continue | 67 | 30 | Cut bridge copy |

**Slide 1 — Welcome.** Copy unchanged. Restyle: headline to
`min(7.2vw,12vh)` weight 200, remove serif italic on "Advantage" and use
`color:var(--accent-bright)` instead, meta lines to 14px mono.

**Slide 2 — Table of Contents.** Cut the three item descriptions to three
words each:
- `01 Recap` — "Where we left off"
- `02 The Shadow AI Role Play` — "Four cases, live"
- `03 Skills Demo` — "Confirmed at 6:30"

Item titles render at `min(3vw,5vh)` weight 300. Active item in
`var(--accent)`; inactive at `var(--text-helper)`.

**Slide 10 — TOC + Continue.** Same TOC treatment, active item 02. Replace
the bridge paragraph with one line: *"June covered why initiatives fail.
Tonight: governance, worked live."* Keep the Continue button.

---

## Part 3 — shadow-ai.html

All 24 slides need type-scale alignment. Copy rewrites below.
Current total 2,157 words / 24 slides = 90 average. Target ≈ 850 / 24 = 35.

### No slide count change

The deck stays at 24 slides. Where a choice arises between more words and
an extra slide, more words wins.

---

### Slide 1 — Cover (45 → 28)

- Eyebrow: `Shadow AI Role Play · Enterprise AI in Practice`
- Headline: `Shadow AI: The Role Play`
- Sub (replaces current two sentences):
  *"Unapproved AI use, already inside the company. Tonight we decide what
  to do about it."*
- People unchanged: Ray Han / IT Director, Lewis Ho / Counsel,
  Arman Tan / Chief Executive
- Headline to `min(7.2vw,12vh)` weight 200; sub to `max(22px,2vw)`

### Slide 2 — The Scenario & Roles (62 → 32)

- Head: *"An internal review found wide, unregulated AI use across the
  company."*
- **Delete the sub-line** — spoken instead
- Role descriptions cut to:
  - Ray Han · IT Director · "Presents each case"
  - Lewis Ho · Counsel · "Frames the options"
  - Arman Tan · Chief Executive · "Makes the call"
- Role names to `min(3vw,5vh)` weight 300; descriptions to `max(18px,1.08vw)`

### Slide 3 — Four Case Studies (72 → 45)

**Use the case-opener headline verbatim for each item.** The same case must
read identically here and when it comes up later. Consistency outranks
brevity on this slide.

- Head: `Four Case Studies`
- Items:
  - `01` Regulated professional work — "Client and patient work drafted on
    consumer artificial intelligence tools."
  - `02` Engineering pipelines — "Engineers wired outside artificial
    intelligence tools into build pipelines, on their own keys."
  - `03` Marketing and communications — "Campaigns drafted on outside
    frontier tools instead of the approved one."
  - `04` Agentic workflows — "Autonomous software on company devices,
    acting with device credentials."
- **Delete the closing paragraph** — spoken instead
- Item titles to `min(3vw,5vh)` weight 300; descriptions to
  `max(18px,1.08vw)`

These four strings must match the case-opener headlines on slides 8, 12,
16, and 20 exactly, character for character.

### Slide 4 — What's at Stake (138 → 48)

Stays as **one slide**. The callout remains on the page.

- Head: *"This Is Not Only a Technology Question"*
- Four items, kicker plus four words each:
  - Financial penalty — "S$1 million, or 10% of turnover"
  - Breach notification — "Three days, 500 people"
  - Personal liability — "Directors, criminal penalties"
  - Disqualification — "Up to five years"
- Callout, kept on the slide and given real weight:
  > *"Penalties attach to negligence — a documented decision is the
  > defense."*
- Kickers to 14px mono; details to `max(22px,2vw)`; callout to
  `min(3vw,5vh)` weight 300 in `var(--accent)`
- Footer: `SOURCE · SINGAPORE PERSONAL DATA PROTECTION ACT · COMPANIES ACT S.157(1)`

Deck stays at 24 slides.

### Slide 5 — The Four Risk Tiers (61 → ~5 on-slide)

**Replace the built tier ladder entirely with Lewis Ho's framework
graphic.** The file is provided as `lewis-risk-tiers.png` (2048×1117,
aspect ratio 1.83, close to 16:9).

- Place at `images/lewis-risk-tiers.png`
- Full-bleed treatment, matching how the June deck handles the Stanford
  Playbook cover on its slide 2 — image fills the slide, minimal chrome
- Eyebrow: `THE METHOD`
- Footer, attribution required: `LEWIS HO · LEXGUARD AI · MODERN AI
  GOVERNANCE FRAMEWORK`
- **Delete** the existing `tiers` slide type, its `.ladder` / `.tier-row` /
  `.tier-tag` / `.tier-ctrl` CSS, and the ladder note

**Two deliberate exceptions to the design system on this slide, both
justified:**

1. The graphic uses green, blue, orange, and red tier coding, which departs
   from the cardinal-only palette. It stays as-is. This is Lewis's
   published intellectual property and its credibility comes from being
   shown unaltered.
2. The graphic is dense. That is acceptable because Lewis talks the room
   through it rather than expecting them to read it. On-slide word count
   effectively drops to the eyebrow and footer.

**Terminology alignment required across the whole deck.** Lewis's graphic
names the tiers *Limited-Risk AI*, *Controlled-Risk AI*, *High-Risk AI*,
and *Critical or Prohibited AI*. The deck currently says "Risk Tier 1 ·
Limited" and similar. Adopt the graphic's naming everywhere — on the
placement matrix, in every risk read result, and in every recommendation
tier label — so the framework the room sees matches the framework it
hears.

### Slide 6 — Case Risk Assessment (103 → ~50, justified exception)

**Title changes from "How We Place It."** Use `Case Risk Assessment`.
Two alternatives if preferred: `Placing a Case` or `How a Case Is Scored`.

The 3×4 matrix is inherently dense and has no June equivalent. Keep all
twelve cells — cutting them breaks the tool.

- **Delete two of the three grid-notes**, keep only the rule line
- Cell text to `max(18px,1.08vw)`, headers to 14px mono
- This slide stays around 50 words; that is acceptable and deliberate

### Slide 7 — Every Case, the Same Shape (37 → 20)

- Keep the four flow steps
- **Delete the flow-note** — spoken instead
- Step text to `min(3vw,5vh)` weight 300

---

### Case slides — pattern applied four times

#### Case opener (42-46 → 28)

Keep tag, headline, why, scale, quote. Cut headline to ~12 words.

- **Case 1:** *"Client and patient work drafted on consumer AI tools."*
  Why: **no approved tool existed**. Quote: *"This is the work we're most
  answerable for."*
- **Case 2:** *"Engineers wired outside AI tools into build pipelines, on
  their own keys."* Why: **speed**. Quote: *"No approved way to do it, so
  people built their own."*
- **Case 3:** *"Campaigns drafted on outside frontier tools instead of the
  approved one."* Why: **the approved tool was weaker**. Quote: *"Not people
  going around a rule. Around a worse tool."*
- **Case 4:** *"Autonomous software on company devices, acting with device
  credentials."* Why: **no approval path existed**. Quote: *"The others are
  about data leaving. This one is about software acting."*

Headline to `min(5vw,8.4vh)` weight 200 — **and remove the current
`max-width:22ch` cap**, which is what confines text to the left quarter of
the slide. Set to `max-width:32ch` and let it fill. Quote to `max(22px,2vw)`.

#### Risk Read (93-131 → ~45)

**Every description must characterize the risk, rather than name the
contents.** "Personal, customer, patient" lists data types and says nothing
about exposure. "Regulated records held in a tool with no contract" states
what the risk actually is. Use a few more words where that is what it takes.

The full paragraph-length explanations still move to speaker notes. What
stays on the slide is a phrase that names the exposure.

**Case 1 — Regulated Professional Work**
- Data — *"Regulated personal and patient records, held in a tool with no
  contract"*
- Consequence — *"A wrong output decides a person's clinical or financial
  outcome"*
- Autonomy — *"A person reviews before release, though no rule requires
  it"*
- Assessment — *"High-Risk overall, set by consequence. The clinical and
  advice work reaches Critical on its own."*

**Case 2 — Engineering Pipelines**
- Data — *"Proprietary source code moving through credentials nobody
  controls"*
- Consequence — *"A flawed suggestion normally gets caught before customers
  see it"*
- Autonomy — *"Tools can ship code to customers with no human checkpoint"*
- Assessment — *"High-Risk, set by autonomy alone. Data on its own would
  suggest far less."*

**Case 3 — Marketing and Communications**
- Data — *"Brand and customer information in a tool with no data terms"*
- Consequence — *"A poor post embarrasses the brand and gets corrected"*
- Autonomy — *"Brand and legal review already stands between draft and
  publication"*
- Assessment — *"Controlled-Risk, set by data. The lightest case
  tonight."*

**Case 4 — Agentic Workflows**
- Data — *"Little of what these agents touch carries sensitivity on its
  own"*
- Consequence — *"A mistake disrupts an internal process rather than a
  customer"*
- Autonomy — *"Acts without approval, on device credentials, and is hard to
  reverse"*
- Assessment — *"Critical, set by autonomy alone. Low data sensitivity
  misleads here."*

Dimension labels to `min(3vw,5vh)` weight 300; descriptions to
`max(20px,1.6vw)` and allowed to wrap to two lines; assessment to
`min(4.6vw,7.6vh)` weight 200.

#### Options (131-153 → 42)

**Two structural changes.**

First, the slide title becomes the situation statement currently sitting in
the vote strip. "How to Address This Risk Tier" is replaced by:

- **Case 1:** *"Staff are drafting client and patient work with consumer
  tools."*
- **Case 2:** *"Engineers connected outside artificial intelligence tools
  directly into the systems that ship our product."*
- **Case 3:** *"Marketing left the official tool for a better one
  outside."*
- **Case 4:** *"An agent runs routine internal work with no sensitive
  data."*

Second, **delete the vote strip entirely** — the `.vote-strip` element, its
CSS, and the `vote` field on every options slide. The room is asked for a
show of hands aloud.

**Delete the "Closes" and "Leaves open" fields from the cards** — Lewis
delivers the trade-offs aloud. Each card carries letter, title, and one
six-word description.

- **Case 1:** A `Block All Use` — "No approved tool, no use." ·
  B `Named Reviewer Sign-Off` — "The responsible professional signs every
  output." · C `Two-Stage Review and Sign-Off` — "Trained reviewer checks,
  professional signs."
- **Case 2:** A `Centralized Connection Gateway` — "Every outside connection
  through one controlled point." · B `Phased Rollout With a Review
  Checkpoint` — "One team first, checkpoint before shipping." ·
  C `Monitor and Tag Retroactively` — "Tag AI-generated code, review later."
- **Case 3:** A `Formalize the Tool Already in Use` — "Put it on a proper
  contract." · B `Upgrade Our Platform to Frontier-Model Capability` —
  "License a frontier model into ours." · C `Monitor Before Deciding` —
  "Track usage before committing to a fix."
- **Case 4:** A `Build an Inventory First` — "What's deployed, what it
  accesses, who owns." · B `Apply Containment Controls` — "Isolated,
  least privilege, approval, kill switch." · C `Suspend All Use, Pending
  Review` — "Block company-wide until a path exists."

Slide title to `min(4.6vw,7.6vh)` weight 200, `max-width:32ch`. Card titles
to `max(22px,2vw)` weight 600; descriptions to `max(18px,1.08vw)`; card
min-height 300px.

#### Recommendation (76-98 → 30)

- **Case 1:** Call: *"Two-Stage Review and Sign-Off."* Why: *"A named
  professional's sign-off is what closes this — applied at volume."*
  Cost: *"A compliant tool, a reviewer role, a sign-off step."*
- **Case 2:** Call: *"Phased Rollout With a Review Checkpoint."* Why:
  *"Visibility and tagging show what happened. Only a checkpoint stops
  it."* Cost: *"Slower rollout, and a named owner throughout."*
- **Case 3:** Call: *"Upgrade Our Platform to Frontier-Model Capability."*
  Why: *"People left for a better tool. The fix is a better tool."*
  Cost: *"A procurement decision now, a product investment behind it."*
- **Case 4:** Call: *"Suspend All Use, Pending Review."* Why: *"Low
  sensitivity, no owner, high reach — the combination that looks safe and
  isn't."* Cost: *"A security review before any agent runs."*

Call to `min(4.6vw,7.6vh)` weight 200. **Why to `max(22px,2vw)`** — it is
load-bearing and currently renders at 15.5px. **Cost to `max(18px,1.08vw)`**
— currently 14px, reads as a footnote.

### Slide 25 — Close (82 → 28)

- Head: *"A Practical, Systematic Way to Address Shadow AI"*
- Body, one line: *"The same three questions produced four different
  defensible answers. The tier decided, not preference."*
- Second line: *"Tonight's risk reads and recommendations are the
  documented record."*
- **Delete** the draft-status tag before the event

---

## Part 4 — Verification

After the changes, re-run a word count per slide and confirm:

- shadow-ai.html averages roughly 35 words across 24 slides
- No rendered text below 14px anywhere in either deck
- No Newsreader / serif font references remain
- Both decks share one `:root` token block
- Case-opener headlines fill the slide width rather than a left column
- All ported June slides in session2-intro.html are untouched

Then push to `preview` on `tpl-ai/gsb-eai-governance-2026` and confirm the
live preview URL renders both decks correctly.

---

## Speaker notes to preserve

The following content is removed from slides but should be captured as
speaker notes, not lost:

- Full risk-read dimension explanations (all four cases, three dimensions
  each) — written for a senior-manager audience, delivered aloud
- The "Closes" and "Leaves open" trade-offs for all twelve options —
  delivered by Lewis when presenting each option set
- The scenario sub-line, the four-case closing paragraph, and the flow-note
