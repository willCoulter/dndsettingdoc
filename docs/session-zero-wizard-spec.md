# Session Zero Wizard — Spec (Full Depth)

A new page, `/session-zero`, that walks a player through a short, step-by-step
character-inspiration flow — not a stat generator, not a rules quiz. Every
question exists to spark a character concept and hand the player a few
evocative lines they can build a backstory from. Every question also needs a
"write your own" escape hatch, because the goal is to inspire, never to box
a concept in.

Tone/voice for all generated copy: same fantasy-Roman, dry-witted, specific
voice already established across the homepage and `/caltara` (see AGENTS.md
"Copy is fantasy-Roman flavored"). Avoid generic fantasy-quiz phrasing
("You feel a strange pull toward..."). Prefer concrete, grounded specifics
in the same register as the Homelands/Gods copy.

## Flow shape

Step-by-step wizard, one question per screen, with:
- A slim progress trail across the top (like a string of beads/sigils — reuse
  the diamond-and-line motif from `Sigil.astro`), showing step N of total and
  which steps are answered.
- Back / Next controls (Next disabled until an option is chosen, unless the
  step is marked optional).
- Each answer chosen gets stored in-memory (client-side JS state object, no
  backend) and feeds later steps (see branching below) and the final Spark
  summary.
- No page reloads between steps — single Astro page, vanilla JS/TS
  controlling which step is visible (similar spirit to the radio-driven
  reveal patterns in `GodsPantheon.astro` / `PowerPaths.astro`, but needs
  real state since later steps branch on earlier answers and the summary
  assembles free-text).

## Step sequence

### 1. Origin

"Where does your story begin?" — the root branch. Options map 1:1 to the
`world` categorization already used in `Homelands.astro` plus a Caltara-born
option:

- **Born in Caltara** — grew up inside the Republic itself.
- **From the New World** — Auryhost, the Dralefon Empire, Nod'el, the Sarian
  Theocracy, the Vorthani Dominion, or the Sea of Bones (reuse the `newWorld`
  list from `Homelands.astro`).
- **From the Old World** — Luth'mar, Ellur, Pharst, Thadmire, Turakai,
  Newgate, or the Splinters (reuse the `oldWorld` list).
- **Answered the Summons directly** — arrived in Caltara specifically because
  of Lysander's letter about Calden (ties into `QuestHook.astro`), regardless
  of homeland — this is a flavor override more than a place, so it's offered
  as a fourth top-level option that then still asks which homeland they left
  behind (folds into step 2's data, doesn't skip it).

If New World or Old World is picked, step 1b asks the player to pick their
specific homeland from that list (reusing the existing `homelands` data:
name, epithet, hook, closer already written in `Homelands.astro` — no new
copy needed here, just surfaced as selectable cards with portrait images).

### 2. Growing Up / Arriving (branches on step 1)

**If Born in Caltara:** "Which of Caltara ever felt like it was actually
yours?" — 5-6 options themed on the districts already written in
`CaltaraDistricts.astro` (Old Wards, Ledger Quarter, Harbor District, Outer
Wards, Old District), each with a short flavor line about what it's like to
have grown up there specifically (distinct from the district's general
description — more personal/nostalgic angle, e.g. "You knew which guild
elders would slip you bread before you knew what a guild seal was").

**If New World / Old World homeland:** "What finally put you on the road to
Caltara?" — 5-6 reason archetypes, homeland-flavored where possible:
- Guild ambition (chasing a seal, a trade, a fortune)
- Exile or disgrace (the door closed behind you)
- A promise or debt owed to someone already in the Republic
- War, refugee status, or displacement (ties to the Vorthani front for
  Vorthani-origin characters specifically)
- Curiosity / the Evarine Academy's Open Question pulling you in
- Faith (following clergy, a god's calling, or a pilgrimage)

Each reason option, when combined with the specific homeland from step 1b,
should ideally get a homeland-specific one-line variant (e.g. "Guild
ambition" reads differently for a Luth'marian than a Sarian). Full depth =
write at least one custom line per (homeland × reason) combination that
plausibly makes sense; skip combinations that don't (e.g. Sarian Theocracy
characters skip "chasing a guild seal" as an option entirely, since that
nation isn't guild-structured the same way — use judgement per homeland).

### 3. A God's Mark

"Which of the Living Gods left a mark on you?" — all twelve gods from
`GodsPantheon.astro` (Turak, Oakheart, Enoch, Elisar & Alder, Karna, Evari,
Auren, Maren, Cass, Iola, Saeth, Thessar), presented as the same fanned card
hand UI already built for that section (reuse the component/CSS, don't
reinvent). Selecting one reveals a short "how it touched you" prompt — not
lore about the god (that's already covered elsewhere), but a personal
hook, e.g.:

- Turak: "Somewhere you swore or witnessed a Standing Challenge that still
  matters to you."
- Saeth: "You've sat through a Quiet Hour, yours or someone else's."

Include an explicit "None — your faith is your own business" option, since
not every character needs a patron god and the wizard shouldn't force one.

### 4. A Defining Moment (new, not sourced from existing sections)

"What's one thing that already happened to your character, before session
one?" — open-ended prompt with 5-6 seed options to overcome blank-page
syndrome (a debt, a broken oath, a death you caused or failed to prevent, a
promise you intend to keep, something you stole that you shouldn't have,
a title or rank you no longer hold) plus a free-text field. This step is
explicitly optional/skippable — some players want to build this at the
table, not alone.

### 5. Spark (summary, not a question)

Compiles the chosen answers into a short flavor paragraph (3-5 sentences),
using the actual selected copy fragments stitched together with light
connective tissue, e.g.:

> "You grew up in the Harbor District, where a dozen languages cross the
> same table by afternoon. Guild ambition put you on the road eventually —
> not out, just further in, toward whatever seal you could earn next. Turak's
> Standing Challenge marked you once, and you haven't forgotten who was
> watching. Somewhere behind you is a debt you intend to repay, on your own
> terms."

Below the paragraph: a "Copy to clipboard" button (no account, no save-to-
server — this is disposable, print-and-go, matching the site's "resource
site" positioning, not a user-accounts product) and a "Start over" reset.
Also surface a compact list of the raw picks (Origin: X, Reason: Y, God:
Z, Moment: W) above or below the paragraph for players who want the data,
not just the prose.

## Data model (for whoever implements)

```ts
type WizardStep =
  | { id: "origin"; options: OriginOption[] }
  | { id: "homeland"; options: HomelandOption[]; dependsOn: "origin" }
  | { id: "grew-up" | "arrival-reason"; options: ReasonOption[]; dependsOn: ["origin", "homeland"] }
  | { id: "god"; options: GodOption[] } // reuses GodsPantheon data
  | { id: "moment"; options: MomentOption[]; optional: true; allowFreeText: true };
```

Each option needs: `id`, `label`, `flavorLine` (used in the summary), and
optionally a `compatibleWith` filter (homeland IDs) for step 2's
homeland-specific variants.

## Visual treatment

Match the established site language:
- `panel-carved` + corner ornaments for the question card.
- `Sigil`-based progress trail.
- Reuse the god-card fan UI verbatim for step 3.
- Reuse homeland portrait images for step 1b cards.
- `animate-rise` for step transitions; a step change should feel like the
  existing scroll-reveal, not a jarring cut.
- Spark summary panel styled like `QuestHook.astro`'s parchment letter — this
  is the "reward" screen, it should feel more precious than the question
  screens (parchment texture already exists in assets: `ParachmentTexture.jpg`).

## Explicitly out of scope for v1

- No persistence/accounts/save-to-server.
- No stat-block or mechanical character sheet generation — this stays purely
  narrative flavor, players still use the actual character sheet separately.
- No forced god selection, no forced homeland-reason combination that
  doesn't make sense — every step needs an escape hatch (free text, "none of
  these," or skip).
