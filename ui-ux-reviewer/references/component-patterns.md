# Component Pattern Best Practices

Concrete, component-level patterns to check during review — complements the principles in
design-principles-themes.md and the checklist items in the main SKILL.md.

## Forms

- Labels top-aligned above inputs (fastest to scan, works at all viewport widths) — not
  placeholder-only (fails WCAG 3.3.2 too, see wcag-checklist.md).
- Mark only what's actually required; don't default every field to required.
- Real-time/inline validation where feasible — don't make users submit to discover an error.
- Errors are specific and actionable ("Password needs 1 number" not "Invalid input").
- Autofocus the first field on single-purpose forms (login, search) — skip on long/complex forms.
- Use `autocomplete` attributes (name, email, tel, etc.) — small effort, real friction reduction.
- **Never erase entered data after a failed submit** — a top-tier "trust destroyer" bug; flag with
  high severity whenever seen.

## Buttons

- One primary button per screen/section — competing primaries create decision friction (Hick's
  Law). Flag multiple equally-weighted CTAs.
- Secondary/tertiary actions can be multiple, but visually subordinate to the primary.
- Danger actions (delete, remove, irreversible) get distinct styling (commonly red) and ideally a
  confirmation step — ties to error prevention heuristic.
- Disabled state used sparingly and only when the reason is obvious or explained nearby; a
  disabled button with no visible reason is a common source of user confusion — prefer an active
  button that explains why an action can't proceed yet, over silent disabling.
- Loading state always indicated (spinner, skeleton, disabled + label change) — never leave a
  clicked button showing no feedback (violates Nielsen's visibility of system status).

## Empty states

Bad: "No data." / blank screen.
Good: explain what's missing, why, and what to do next — e.g. "No projects yet — create your
first project to get started," ideally paired with a clear CTA and optionally a simple
illustration. Empty states are a first-impression moment for new users — treat as real design
surface, not an afterthought.

## Error messages

- Explain what went wrong and how to fix it, not just a code ("Error 403" alone is a fail).
- For validation errors, list requirements concretely ("8+ characters, one number, one symbol")
  rather than a single vague "invalid password" message.
- Pair with `aria-describedby`/`aria-live` so assistive tech announces the message (WCAG 4.1.3).

## Microinteractions

Small feedback moments that should exist, not decorate: hover/press state changes, toggle
transitions, pull-to-refresh, typing indicators, success checkmarks after an action completes.
Absence of these often reads as "unfinished" even when core functionality works. Keep duration
short (100-300ms) with natural easing — avoid excessive bounce/elastic effects on everything.

## UX writing (microcopy)

- Button labels describe the specific action: "Create Project" not "Submit," "Save Changes" not
  "Execute." Generic verbs force the user to infer what will actually happen.
- Be human and specific over generic and terse — but don't over-explain either; match the
  product's overall tone.

## Performance as UX (not just a backend concern)

- Skeleton screens generally feel faster than blank spinners because they preview structure while
  content loads — prefer skeletons for content-heavy views, spinners are fine for short waits.
- Flag any interaction with no feedback for more than ~400ms (Doherty Threshold, see
  heuristics.md) as a perceived-performance UX issue even if the actual load time is reasonable.