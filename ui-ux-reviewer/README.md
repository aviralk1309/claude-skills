# UI/UX Reviewer

A Claude skill that reviews interfaces like a senior product designer — not just a developer
running a linter. Covers layout, accessibility (WCAG 2.2), color contrast, mobile responsiveness,
user flows, design consistency, and visual/creative design quality, for a worldwide audience.

## What it does

Give it a screenshot, a URL, or component code and ask for a review, an audit, or feedback on
whether something "looks good" or "feels off." It walks a fixed 9-step review order and returns
a structured report — findings tagged by severity (Blocker / Major / Minor / Suggestion), plus
a scorecard — instead of a vague, vibes-based paragraph.

It leads with a simple frame: work the **UX Pyramid** bottom-up (Solves Real Problem → Easy to
Use → Easy to Learn → Feels Good → Looks Good), and apply the **Golden Rule** — if a user has to
stop and think "what does this do?", that's a real finding, regardless of how polished the visual
layer is.

## When it triggers

Use it (or expect it to trigger) any time you:
- Share a screenshot, URL, or component and ask for feedback/a review/an audit
- Ask "is this accessible," "does this feel mobile friendly," "why does this look dated"
- Ask for help picking a color scheme, theme, or typography direction
- Ask about touch targets, contrast, responsive breakpoints, or user flow friction

It should **not** trigger for pure logic/backend bugs with no visual or interaction surface.

## Structure

```
ui-ux-reviewer/
├── SKILL.md                              # entry point: review order, checklist, principles
├── CHANGELOG.md
├── README.md
├── evals/
│   └── evals.json                        # 5 eval cases for regression-checking the skill
└── references/
    ├── wcag-checklist.md                 # WCAG 2.2 AA, by POUR, with SC numbers
    ├── contrast-formulas.md              # WCAG ratio (with code) + APCA
    ├── heuristics.md                     # Nielsen's 10, UX laws, Doherty Threshold
    ├── responsive-standards.md           # breakpoints, touch targets, container queries
    ├── design-principles-themes.md       # hierarchy, color, type, Gestalt, trends, themes
    ├── component-patterns.md             # forms, buttons, empty states, errors, microcopy
    ├── global-considerations.md          # worldwide i18n, regional standards, low-end devices
    └── report-template.md                # fixed output structure
```

`SKILL.md` stays lean and points into `references/` — only the relevant reference file gets
pulled into context for a given review, so a contrast-only question doesn't load the entire
heuristics library.

## Review order (summary — full detail in SKILL.md)

1. Context gather (device/audience/locale, input type)
2. Automated-rule pass (axe-core-equivalent reasoning)
3. Contrast pass (exact ratio computation)
4. Heuristic pass (Nielsen's 10 + UX laws)
5. Responsive pass (breakpoints, touch targets, zoom/reflow)
6. Visual design pass (hierarchy, color, type, Gestalt, trends)
7. Component pattern pass (forms, buttons, empty states, errors, microcopy)
8. Consistency pass (design tokens, component reuse)
9. Global/i18n pass (regional standards, script/RTL, text expansion, device diversity)

## Design decisions worth knowing

- **Worldwide by default.** No single region's norms are assumed. WCAG 2.2 AA is used as the
  floor because it meets or exceeds every regional accessibility mandate currently referenced
  (US/EU/UK/India/Canada/Australia — see `references/global-considerations.md`).
- **Compliance is the floor, not the goal.** Every reference file separates "what's legally/
  technically required" from "what actually makes the interface good" — the visual design and
  component pattern passes exist specifically so a review doesn't stop at "passes WCAG."
- **AI-generated UI is treated as a first-class review target**, not an edge case — see the
  dedicated note in `global-considerations.md`, since a growing share of what this skill reviews
  will be AI-scaffolded code with plausible-looking but subtly wrong conventions.

## Running the evals

```bash
python -m scripts.run_loop \
  --eval-set evals/evals.json \
  --skill-path . \
  --model <model-id>
```
(via the `skill-creator` skill's tooling — see its `SKILL.md` for details on Claude Code/Cowork.
On Claude.ai, run each eval prompt manually against the skill and compare output to
`expectations`.)

## Status

v1.0.0 — structurally validated (`quick_validate.py`), one eval manually spot-checked (login-form
case: correct WCAG 3.3.2 citation, correct contrast ratio computed and independently verified).
Remaining 4 evals not yet run at scale — recommended before wider distribution.

## License

Add your repo's license here (e.g. MIT) before publishing.