# Global / Worldwide Considerations

Default assumption: audience is worldwide, multi-device, multi-ability, multi-language unless
user states otherwise. Don't default to a single region's norms.

## Regional accessibility mandates (cite the relevant one if audience/region is known)

| Region | Standard |
|---|---|
| USA | ADA (Title II/III case law references WCAG 2.1 AA), Section 508 for federal |
| EU | EN 301 549 (references WCAG 2.1/2.2 AA), enforced via European Accessibility Act (2025) |
| UK | Public Sector Bodies Accessibility Regulations 2018, references WCAG 2.1 AA |
| India | GIGW 3.0 (govt sites), IS 17802 (national standard, aligned WCAG 2.1 AA) |
| Canada | AODA (Ontario), references WCAG 2.0/2.1 AA |
| Australia | DDA + WCAG 2.1 AA referenced in govt digital standards |

Default recommendation regardless of region: **WCAG 2.2 AA** — it's the strictest commonly-cited
baseline and satisfies or exceeds most regional mandates above.

## Language & script

- Don't assume Latin script or LTR reading only. If RTL (Arabic, Hebrew) is in scope, check
  mirrored layout (nav, icons, form alignment), not just text direction.
- Font stacks must render the target scripts correctly (Devanagari, CJK, Arabic, Cyrillic, etc.)
  — flag font-family fallback chains that only cover Latin.
- Text expansion: translated UI strings commonly run 30-40% longer than English (German, Finnish
  especially) — flag fixed-width buttons/labels that will truncate on translation.
- Locale-aware formatting: dates, currency, number separators (1,000.00 vs 1.000,00), name order
  (given/family name assumptions break in many cultures) — flag hardcoded US-centric formats.

## Device & network diversity

- Large global user base is on mid/low-end Android devices and non-fiber networks (3G/4G, spotty
  connectivity) — flag heavy JS bundles, unoptimized images, no offline/skeleton loading states
  as real UX failures, not just performance nitpicks.
- Core Web Vitals — **INP (Interaction to Next Paint)** replaced FID as the responsiveness metric
  (since March 2024) — flag long main-thread tasks that block interaction, not just load time.

## Age & literacy diversity

- Design for a range of digital literacy and age, not just tech-fluent early adopters:
  recognition over recall, avoid icon-only nav without text labels, avoid gesture-only
  interactions with no visible alternative, larger base font sizes and touch targets help both
  older users and low-end touchscreen accuracy.

## AI-generated UI risk (relevant since this skill will often review AI-scaffolded code)

AI-generated components frequently look plausible but violate real conventions — inconsistent
spacing scale, contrast that "looks fine" in isolation but fails on actual backgrounds, ARIA
attributes present but semantically wrong, hover-only interactions with no touch/keyboard
equivalent. Specifically check for these when the code appears AI-generated (uniform generic
naming, boilerplate comments, inconsistent conventions within the same file).

## Quick global-readiness checklist

- [ ] No hardcoded region-specific date/currency/name-order formats
- [ ] Font stack covers target scripts, not just Latin
- [ ] Fixed-width text containers tested against 30-40% translation expansion
- [ ] Works reasonably on a throttled 3G/mid-range-Android simulation
- [ ] No gesture-only or hover-only interactions without alternative
- [ ] Relevant regional accessibility standard identified if audience is known