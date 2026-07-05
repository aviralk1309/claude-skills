# Contributing

Thanks for considering a contribution. This skill is a curated, opinionated review framework —
the bar for merging is "does this reflect real production UX/accessibility practice and hold up
under senior-product-designer-level scrutiny," not "is this technically true in some context."

## Ways to contribute

- **Fix an error** — factual mistake (wrong WCAG SC number, outdated metric like a stale Core Web
  Vitals reference), outdated recommendation, broken cross-reference between files.
- **Add a gap** — a topic within scope (see README `Covers` list) that's missing or thin.
- **Improve triggering** — better positive/negative examples for `SKILL.md`'s description, based
  on real cases where the skill over- or under-triggered.
- **New reference file** — a new domain area (e.g. voice UX, data-viz/dashboard-specific
  patterns, native mobile platform conventions). Open an issue first to discuss scope before
  writing 3000 words nobody asked for.
- **New/updated regional standard** — accessibility law changes by region (e.g. a new EU
  directive, an updated national standard). Update `global-considerations.md`'s table and cite
  the source.

## Style rules (enforced in review)

Match the existing reference files:

- **Cite the actual standard**, don't just assert "best practice" — WCAG SC number, named law
  (Fitts's, Hick's, Jakob's), or named principle (Gestalt proximity, 60-30-10 rule), not "this
  isn't great."
- **Every recommendation states its cost/tradeoff**, not just its benefit. If a pattern trades
  something off (e.g. glassmorphism vs contrast), say so — no silver bullets.
- **Concrete over abstract**: exact pixel values, contrast ratios, code snippets, table
  breakpoints over vague descriptions of them.
- **Severity language stays consistent** with `report-template.md`'s four tiers (Blocker / Major
  / Minor / Suggestion) — don't invent new severity words in a reference file.
- Keep each reference file self-contained but cross-link where another file covers a related
  angle (e.g. `component-patterns.md` linking to `wcag-checklist.md` for the WCAG SC behind a
  form-label rule). Use `` `filename.md` `` inline references, not vague "see elsewhere."
- No vendor-specific pitches — name real tools as examples (axe-core, Lighthouse, Stark) but keep
  the principle vendor-agnostic.
- Trend content (in `design-principles-themes.md`) must be clearly separated from durable
  principles — label it "current trend" so it ages visibly and gets revisited, not treated as
  permanent doctrine.

## Adding a new reference file — checklist

1. Open an issue describing scope and how it doesn't overlap an existing file.
2. Add it to the review order / reference file list in `SKILL.md`.
3. Add it to the `references/` folder listing in `README.md` and the `Covers` section.
4. Add at least 2 positive eval cases (should trigger and use the new content) and 1 negative
   case (superficially adjacent but shouldn't) to `evals/evals.json` — see `evals/README.md`.

## Testing changes

This skill doesn't have executable code (aside from illustrative snippets), so "testing" means:

- **Content accuracy** — if you're changing a recommendation, cite why (WCAG spec, a named law,
  a platform HIG doc) in the PR description, not just personal experience.
- **Triggering accuracy** — if you touch `SKILL.md`'s description, run the cases in
  `evals/evals.json` before and after your change and note the result in the PR.
- **Formula changes** — if you touch `contrast-formulas.md`'s code, verify it against a couple of
  known RGB pairs independently (e.g. in a Python/JS REPL) before merging, not just by reading it.
- **Cross-reference integrity** — if you rename a file or section, grep the whole `references/`
  folder for other files pointing at it and update them in the same PR.

## PR review priorities

1. Correctness — is the cited standard/formula/law actually right, not just plausible-sounding
2. Overcompliance-as-substitute risk — does this let a reviewer stop at "passes WCAG" instead of
   asking whether it's actually good (the skill explicitly treats compliance as floor, not ceiling)
3. Consistency — severity language, cross-links, structure
4. Wording/polish

## Code of conduct

Be direct about disagreements on design opinions, be kind about people. Assume good faith — a lot
of visual-design judgment is genuinely contested; cite reasoning (a law, a standard, a documented
pattern) instead of asserting taste as fact.