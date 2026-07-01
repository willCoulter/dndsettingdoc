## Project

Elderan is a marketing/resource site for a tabletop D&D campaign of the same
name. It promotes the campaign and hosts lore and character-creation
resources for the players. Currently a single homepage
(`src/pages/index.astro`); more pages (lore codex, factions, sessions, etc.)
are expected to follow the same design language.

## Design language

Visual direction: a Path-of-Exile-style dark gaming landing page fused with
"ornate Rome meets generic high fantasy" — laurels, marble, weathered gold
leaf, column/heraldry motifs, carved-panel borders, alongside fantasy banners
and runes. Keep new pages/sections consistent with this:

- **Palette** (`src/styles/global.css`, Tailwind v4 `@theme`): deep
  obsidian/charcoal backgrounds (`--color-obsidian*`, `--color-charcoal`,
  `--color-stone*`), aged gold/bronze accents (`--color-gold*`,
  `--color-bronze*`), parchment cream text (`--color-parchment*`), plus
  faction accent colors (`--color-blood*`, `--color-verdigris`).
- **Type**: `--font-display` (Cinzel) for headings/labels/eyebrows,
  `--font-deco` (Cinzel Decorative) for rare ornamental moments,
  `--font-body` (Crimson Pro) for body copy. Headings are generally
  uppercase with wide tracking (`tracking-roman`, `tracking-[0.2em]`+).
- **Reusable utility classes** in `global.css`: `.panel-carved` +
  `.corner-ornament` (carved/engraved panel with corner brackets — pair with
  the four `.corner-ornament.{tl,tr,bl,br}` spans), `.bg-marble` /
  `.bg-grain` (atmospheric background texture), `.text-gold-gradient`
  (gradient gold text clip), `.eyebrow` (small tracked-out gold label above
  headings), `.animate-rise` (staggered fade-up reveal, use inline
  `animation-delay` to stagger siblings).
- **Components** live in `src/components/` as `.astro` files (no React
  needed for this site so far — hover/scroll states are plain CSS).
  `Sigil.astro` is the small diamond-and-line section divider used under
  most section headings. Follow the existing section pattern: eyebrow →
  `font-display` heading with one gold-gradient word → `<Sigil />` → intro
  copy → content grid/panels.
- **Hero imagery**: campaign art lives in `src/assets/` and should be
  rendered via Astro's `<Image>` from `astro:assets` (not a plain `<img>`)
  for optimization, with a dark gradient + grain overlay so text stays
  readable on top.
- Copy is fantasy-Roman flavored (legions, the Senate, oracles, provinces,
  the "Eagle Throne") — match that tone for new placeholder content.

## Development

When starting the dev server, use background mode:

```
astro dev --background
```

Manage the background server with `astro dev stop`, `astro dev status`, and `astro dev logs`.

## Documentation

Full documentation: https://docs.astro.build

Consult these guides before working on related tasks:

- [Adding pages, dynamic routes, or middleware](https://docs.astro.build/en/guides/routing/)
- [Working with Astro components](https://docs.astro.build/en/basics/astro-components/)
- [Using React, Vue, Svelte, or other framework components](https://docs.astro.build/en/guides/framework-components/)
- [Adding or managing content](https://docs.astro.build/en/guides/content-collections/)
- [Adding styles or using Tailwind](https://docs.astro.build/en/guides/styling/)
- [Supporting multiple languages](https://docs.astro.build/en/guides/internationalization/)
