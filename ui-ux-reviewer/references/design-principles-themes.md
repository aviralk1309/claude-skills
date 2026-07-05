# Design Principles & Theme Direction (2026 market)

This is the pass that separates "technically compliant" from "actually looks good." Use it to
give creative, opinionated feedback — not just pass/fail checklist items.

## Core visual principles

### Hierarchy
- Size, weight, color, and position should guide the eye in the intended order (usually:
  primary action/headline → supporting info → secondary actions).
- Flag "flat hierarchy" — everything same size/weight, nothing pulls focus.
- Bigger contrast between heading and body sizes reads as more confident/editorial; smaller
  contrast reads as more utilitarian/dense (dashboards, admin tools). Match to product type.

### Spacing & grid
- 8pt spacing scale (4, 8, 12, 16, 24, 32, 48, 64...) is the de facto industry standard —
  flag arbitrary values (13px, 22px) as inconsistency smells.
- Whitespace is a design tool, not empty space to fill — cramped layouts read as low-quality
  even when every element is individually fine.

### Color theory
- **60-30-10 rule**: 60% dominant/neutral, 30% secondary, 10% accent — gives balanced palettes.
  Flag palettes with too many competing accent colors (usually >2 accents = visual noise).
- Semantic color consistency: same color = same meaning everywhere (red always = error/danger,
  never also used as a brand accent elsewhere in the same product — causes misread).
- Accessible palettes: build from a base color and check contrast at each step, don't pick
  colors purely by eye then patch contrast later.

### Typography
- Limit to 2 type families max (one for display/headings, one for body) — flag 3+ fonts as
  inconsistent/amateur signal.
- Type scale should follow a ratio (1.125, 1.25, 1.333, 1.5 are common), not ad hoc sizes.
  Fluid clamp-based scales (see responsive-standards.md) are current best practice.
  Variable fonts (single file, multiple weights) are now standard — reduces load + gives finer
  weight control than fixed 400/700 jumps.
- Line length: 45-75 characters per line for body text is the readability sweet spot — flag
  full-width body text on wide desktop layouts.

### Motion
- Motion should communicate state change (something appeared, something is loading, something
  succeeded) — not decorate. Flag animation that exists "because it's cool" with no functional
  purpose, and always pair with `prefers-reduced-motion` fallback.

## Gestalt principles (how humans group visual info — use to critique layout, not just spacing)

- **Proximity** — elements placed close together read as related (e.g. label directly above its
  input, not floating equidistant between two fields).
- **Similarity** — same color/shape/style reads as same category/function. Flag inconsistent
  styling for elements that are supposed to be equivalent (e.g. one card rounded, another sharp).
- **Common region** — a shared boundary (card, border, background fill) groups content inside it
  as one unit — explains why cards work so well for grouping.
- **Continuity** — the eye follows lines/alignment; flag layouts with broken alignment that force
  the eye to jump unnecessarily.
- **Closure** — humans mentally complete incomplete shapes — enables minimal/line-art icons to
  still read clearly; don't over-simplify past the point of recognition.
- **Figure/ground** — the important element must visually separate from its background; flag low-
  contrast CTAs that blend into a busy background instead of popping forward.

Use these to explain *why* a layout feels off, not just that it does — "spacing between label and
input is wider than spacing between unrelated fields, so proximity cue misleads the eye" is a much
more actionable finding than "layout feels weird."

## Current design trends worth knowing (2026 market context)

Use these as informed suggestions, not mandates — trends age, principles above don't. Cite when
relevant, don't force a trend onto a product where it doesn't fit.

- **Dark mode as default expectation** — not a toggle buried in settings; increasingly a first-
  class, equally-designed experience (not just inverted colors).
- **Bento grid layouts** — modular card grids of varying sizes (popularized by Apple product
  pages) for feature/dashboard overviews — good for showcasing multiple content types at once.
- **Big, confident typography** — oversized display type as a hero design element, replacing
  heavy stock photography/illustration as the primary visual interest.
- **Subtle grain/texture + soft gradients** — countering the flat, sterile "corporate Memphis"
  look that dominated 2018-2022; adds warmth without heavy skeuomorphism.
- **Glassmorphism (used sparingly)** — frosted-glass translucent panels for overlays/nav — flag
  overuse since it can hurt contrast/readability if not paired with a solid contrast check.
- **AI-native interface patterns** — inline AI affordances (ghost-text suggestions, streaming
  responses, command palettes/`⌘K` style quick actions) becoming expected in productivity tools.
- **Reduced chrome, content-first** — less decorative UI, more focus on content itself (visible
  in reading apps, note tools, minimal dashboards).
- **Micro-interactions with restraint** — small delightful feedback (button press, toggle
  transition) but not gratuitous — pairs with the "motion with purpose" principle above.

## Theme direction ideas (give when user asks "what theme/color scheme should I use")

Frame suggestions around product type, not just aesthetic preference:

- **Fintech / trust-heavy product** — deep blue/navy + single confident accent (green or teal),
  generous whitespace, restrained motion — conveys stability.
- **Consumer social/creative tool** — warmer palette, bolder accent color, more playful motion,
  higher contrast typography — conveys energy/approachability.
- **Productivity/B2B SaaS** — neutral base (gray/slate), single brand accent, dense-but-clear
  layout, dark mode parity important (power users often prefer dark) — conveys efficiency.
- **Government/public service** — must prioritize clarity and accessibility over aesthetic
  novelty; higher contrast than typical AA minimum, plain language, conservative type choices,
  larger base font size — conveys reliability, works for widest possible audience.
- **Healthcare** — calming, desaturated palette (avoid alarming reds except for true emergency
  states), high legibility, avoid clinical coldness — conveys care + trust simultaneously.

When reviewing an existing theme, name what emotion/positioning the current choices communicate,
then check if that matches the product's actual positioning — mismatches (e.g. a serious fintech
tool using a playful rounded-mascot aesthetic) are worth flagging even if technically consistent.