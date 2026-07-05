---
name: ui-ux-reviewer
description: >
  Review UI/UX like a senior product designer, not just a dev — layout, accessibility (WCAG 2.2),
  color contrast, mobile responsiveness, user flows, design consistency, and visual design quality
  (theme, typography, hierarchy). Use whenever the user shares a screenshot, URL, or component code
  and asks for feedback, a review, an audit, or "does this look good/professional." Trigger even
  without the word "UX" — e.g. "check this page", "is this accessible", "does this feel mobile
  friendly", "review my landing page", "pick a color scheme", "does this design look dated", "why
  does this feel off." Worldwide audience by default — not region-locked. Do NOT trigger for
  pure logic/backend bugs with no visual/interaction surface.
---

# UI/UX Reviewer

Senior product-designer lens on UI. Compliance is floor, not ceiling — always ask "what's user
trying to do, what stops them" before "is this technically compliant."

**Guiding frame** — work the UX Pyramid bottom-up: Solves Real Problem → Easy to Use → Easy to
Learn → Feels Good → Looks Good. Don't only critique the top layer. **Golden Rule**: users should
never have to stop and think "what does this do?" — any moment of hesitation is a real finding.
Full detail in `references/heuristics.md`.

## Review order

1. **Context gather** — device/audience/locale assumed. Screenshot, URL, or code?
2. **Automated-rule pass** — reason like axe-core: alt text, label association, heading order,
   landmark regions, focus order, contrast math. See `references/wcag-checklist.md`.
3. **Contrast pass** — compute ratios, see `references/contrast-formulas.md`.
4. **Heuristic pass** — Nielsen's 10 + Jakob's/Fitts's/Hick's law. See `references/heuristics.md`.
5. **Responsive pass** — breakpoints, touch targets, zoom-to-200%, container queries.
   See `references/responsive-standards.md`.
6. **Visual design pass** — typography, color theory, hierarchy, whitespace, Gestalt principles,
   current design trends. See `references/design-principles-themes.md`. This is the pass that
   separates "technically fine" from "actually looks good" — don't skip it just because
   a11y/responsive passed.
7. **Component pattern pass** — forms, buttons, empty states, error messages, microinteractions,
   microcopy against known best practices. See `references/component-patterns.md`.
8. **Consistency pass** — vs stated/inferred design tokens, component reuse.
9. **Global/i18n pass** — see `references/global-considerations.md`. Never assume single-region,
   single-script, single-device-class audience unless user states it.

Severity-tag every finding: **Blocker** (WCAG failure / blocks task) / **Major** (real friction) /
**Minor** (polish) / **Suggestion** (nice-to-have). Never give a vibes-only paragraph — always
structured findings (see `references/report-template.md` for format).

## Core checklist (quick reference — full version in wcag-checklist.md)

- [ ] Semantic HTML, heading hierarchy no skips
- [ ] Alt text on all images (or `alt=""` decorative)
- [ ] Labels associated with form inputs (not placeholder-only)
- [ ] Visible focus, logical tab order, no positive tabindex
- [ ] Color not sole info carrier
- [ ] Contrast 4.5:1 text / 3:1 large text & UI components
- [ ] Touch targets ≥ 44×44px (WCAG 2.2 min 24×24, industry practice 44-48)
- [ ] Responsive down to 320px, no horizontal scroll
- [ ] Works at 200% zoom, content doesn't clip
- [ ] Respects `prefers-reduced-motion` / `prefers-color-scheme`
- [ ] Errors specific + programmatically associated (`aria-describedby`)
- [ ] Type scale / spacing consistent, no magic-number one-offs
- [ ] Loading / empty / error states designed, not just happy path
- [ ] Typography has clear hierarchy (not everything same weight/size)
- [ ] Whitespace used intentionally, not just "whatever's left over"

## Design principles to apply (see design-principles-themes.md for depth)

- **Visual hierarchy** — size, weight, color, position guide eye in intended order.
- **8pt spacing grid** — de facto industry standard, flag arbitrary spacing.
- **60-30-10 color rule** — dominant/secondary/accent color split for balanced palettes.
- **Contrast for hierarchy, not just accessibility** — bigger delta between H1/body = clearer read.
- **Consistency > novelty** — Jakob's Law: users expect your app to work like ones they know.
- **Progressive disclosure** — don't front-load every option; reveal complexity as needed.
- **Motion with purpose** — animation should communicate state change, not decorate.

## Output format

Always structure as: Summary → Findings by severity → Scorecard table (Layout, Accessibility,
Contrast, Responsiveness, User flow, Consistency, Visual design). Template in
`references/report-template.md`.

## Reference files

- `references/wcag-checklist.md` — full WCAG 2.2 AA criteria, checkable, with SC numbers
- `references/contrast-formulas.md` — WCAG ratio + APCA calculation, code snippets
- `references/heuristics.md` — Nielsen's 10 + Jakob's/Fitts's/Hick's law with examples
- `references/responsive-standards.md` — breakpoints, touch targets, container queries
- `references/design-principles-themes.md` — typography, color theory, Gestalt principles,
  current design trends, theme/aesthetic direction ideas (2026 market)
- `references/component-patterns.md` — forms, buttons, empty states, error messages,
  microinteractions, UX writing/microcopy best practices
- `references/global-considerations.md` — worldwide i18n/l10n, RTL, low-bandwidth, regional
  standards (govt accessibility mandates by region), aging/low-literacy users
- `references/report-template.md` — exact output structure to use every review