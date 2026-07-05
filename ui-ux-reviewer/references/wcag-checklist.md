# WCAG 2.2 AA Checklist

Organized by POUR (Perceivable, Operable, Understandable, Robust). SC = Success Criterion number.
Use this to cite specific criteria in findings instead of vague "accessibility issue."

## Perceivable

- **1.1.1 Non-text Content** — all images/icons have alt text or `alt=""` if decorative.
- **1.3.1 Info and Relationships** — semantic markup conveys structure (headings, lists, tables
  use real tags, not styled divs).
- **1.3.4 Orientation** — content works in both portrait and landscape, don't lock orientation.
- **1.3.5 Identify Input Purpose** — `autocomplete` attributes on common fields (name, email, etc).
- **1.4.3 Contrast (Minimum)** — 4.5:1 normal text, 3:1 large text (≥24px or ≥19px bold).
- **1.4.4 Resize Text** — usable at 200% zoom without loss of content/function.
- **1.4.10 Reflow** — no horizontal scroll at 320px width / 400% zoom equiv.
- **1.4.11 Non-text Contrast** — 3:1 for UI component boundaries, icons, graphs.
- **1.4.12 Text Spacing** — no content loss when user overrides line-height/spacing.
- **1.4.13 Content on Hover/Focus** — tooltips dismissible, hoverable, persistent.

## Operable

- **2.1.1 Keyboard** — everything operable via keyboard alone.
- **2.4.3 Focus Order** — logical, matches visual order.
- **2.4.6 Headings and Labels** — descriptive, not "click here."
- **2.4.7 Focus Visible** — visible focus indicator, never `outline: none` without replacement.
- **2.4.11 Focus Not Obscured (Minimum)** — [2.2 new] focused element not hidden behind sticky
  headers/footers.
- **2.5.7 Dragging Movements** — [2.2 new] drag interactions have a non-drag alternative (buttons).
- **2.5.8 Target Size (Minimum)** — [2.2 new] targets ≥24×24px unless inline/equivalent alt exists.
  Industry practice goes further: 44×44 (Apple HIG) / 48×48dp (Material) for primary actions.

## Understandable

- **3.2.6 Consistent Help** — [2.2 new] help mechanism (chat, contact, FAQ) in same relative
  location across pages.
- **3.3.1 Error Identification** — errors described in text, not color/icon alone.
- **3.3.2 Labels or Instructions** — inputs have visible labels, not placeholder-only.
- **3.3.7 Redundant Entry** — [2.2 new] don't force re-entry of info already provided in same flow.
- **3.3.8 Accessible Authentication (Minimum)** — [2.2 new] no cognitive-function-only test
  (e.g. puzzle memorization) as sole login method without alternative.

## Robust

- **4.1.2 Name, Role, Value** — custom widgets (custom dropdowns, modals) expose correct ARIA
  role/state, prefer native elements first.
- **4.1.3 Status Messages** — dynamic updates (form submitted, cart updated) announced via
  `aria-live`, not just visual toast.

## Automated-tool-equivalent mental model

When no live tool access, reason through this order (mirrors axe-core rule priority):
1. Missing alt text / empty links / empty buttons
2. Label-input association
3. Heading order skips (h1→h3 with no h2)
4. Landmark regions present (`<main>`, `<nav>`, `<header>`) — not everything a `<div>`
5. Color contrast math on all text/background pairs
6. Duplicate IDs, invalid ARIA attribute values
7. Focus order vs DOM order vs visual order mismatch