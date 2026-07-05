# Changelog

All notable changes to the `ui-ux-reviewer` skill are documented here.

## [1.0.0] - 2026-07-05

### Added

- Initial release of `ui-ux-reviewer` skill.
- Core `SKILL.md` with 9-step review order: context gather → automated-rule pass → contrast pass
  → heuristic pass → responsive pass → visual design pass → component pattern pass → consistency
  pass → global/i18n pass.
- Guiding frame: UX Pyramid (Solves Real Problem → Easy to Use → Easy to Learn → Feels Good →
  Looks Good) and the Golden Rule ("users should never have to stop and think").
- `references/wcag-checklist.md` — WCAG 2.2 AA criteria organized by POUR, with SC numbers, plus
  an axe-core-equivalent manual reasoning order for when no automated tool is available.
- `references/contrast-formulas.md` — WCAG 2.x relative-luminance contrast ratio formula (with
  working JS implementation) and APCA as a supplementary perceptual metric.
- `references/heuristics.md` — Nielsen's 10 heuristics, Jakob's/Fitts's/Hick's/Miller's laws,
  Peak-End Rule, and Doherty Threshold (400ms response guidance).
- `references/responsive-standards.md` — breakpoint reference table, container queries, touch
  target sizing (WCAG 2.5.8 + platform HIG guidance), fluid typography, zoom/reflow requirements.
- `references/design-principles-themes.md` — visual hierarchy, spacing/color/typography
  principles, Gestalt principles (proximity, similarity, common region, continuity, closure,
  figure/ground), 2026 design trend context, and theme direction guidance by product type
  (fintech, consumer/creative, B2B SaaS, government, healthcare).
- `references/component-patterns.md` — forms, buttons, empty states, error messages,
  microinteractions, and UX writing/microcopy best practices.
- `references/global-considerations.md` — worldwide i18n/l10n guidance: regional accessibility
  mandates (US/EU/UK/India/Canada/Australia), RTL/script support, text-expansion risk, low-end
  device and network considerations, INP as the current Core Web Vitals responsiveness metric,
  and AI-generated-UI failure patterns.
- `references/report-template.md` — fixed output structure (Summary, Findings by severity,
  Scorecard, Global readiness) used on every review.
- `evals/evals.json` — 5 eval cases covering: missing labels/contrast/copy on a login form,
  icon-only nav touch targets, data-loss-on-error severity calibration, color palette /
  60-30-10 rule application, and worldwide i18n readiness (currency/date/text-expansion).

### Validated

- Passed `quick_validate.py` structural check.
- Manually ran eval #1 (login form) against the skill; confirmed correct WCAG 3.3.2 citation,
  correct contrast ratio computation (1.71:1, verified independently in Python against the
  documented formula), and correct escalation to Blocker severity.

### Scope notes

- Worldwide/multi-region audience by default — not locked to any single country's standards.
  WCAG 2.2 AA used as the universal floor since it meets or exceeds all regional mandates
  currently referenced.
- Learning-roadmap content (recommended books, general career progression, advanced HCI/JTBD/
  spatial-computing topics) intentionally excluded — this skill is scoped to executing reviews,
  not teaching UX as a discipline.
