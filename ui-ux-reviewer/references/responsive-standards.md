# Responsive & Mobile Standards

## Breakpoints (Tailwind-convention, widely adopted as de facto industry default)

| Name | Min width | Typical device |
|---|---|---|
| (base) | 0 | Small phones |
| sm | 640px | Large phones |
| md | 768px | Tablets (portrait) |
| lg | 1024px | Tablets (landscape) / small laptops |
| xl | 1280px | Laptops |
| 2xl | 1536px | Large desktop |

Design mobile-first: base styles for smallest screen, add complexity upward. Flag desktop-first
code (media queries only using `max-width`) as a smell for 2026 baseline.

## Container queries

`@container` now broadly supported (Chrome/Edge/Firefox/Safari all shipped). Prefer over pure
viewport media queries when a component's layout should respond to its container, not the whole
page (e.g. a card that looks different in a sidebar vs full-width). Flag components that only
use viewport-based breakpoints if they're reused in variable-width containers.

## Touch targets

- WCAG 2.2 SC 2.5.8 minimum: 24×24px.
- Apple HIG recommendation: 44×44pt.
- Material Design recommendation: 48×48dp.
- Practical guidance for review: flag anything under 40px for primary actions, and check spacing
  between adjacent targets (min ~8px gap to prevent mis-taps).

## Fluid typography

Prefer `clamp(min, preferred, max)` over fixed breakpoint jumps for type scale:
```css
font-size: clamp(1rem, 0.9rem + 0.5vw, 1.25rem);
```
Flag hardcoded `font-size` per breakpoint as more brittle than a fluid clamp scale.

## Zoom & reflow

- Must remain usable at 200% browser zoom (WCAG 1.4.4) — no clipped content, no broken layout.
- Must reflow to single column at 320px CSS width without horizontal scroll (WCAG 1.4.10),
  except content that's inherently 2D (data tables, images, maps).

## Motion & theme preferences

```css
@media (prefers-reduced-motion: reduce) {
  * { animation: none !important; transition: none !important; }
}
@media (prefers-color-scheme: dark) {
  /* dark theme tokens */
}
```
Flag any UI with animation and no `prefers-reduced-motion` handling, and any UI without a dark
theme option in 2026 — it's now a baseline expectation, not a premium feature.

## Testing checklist for review

- [ ] 320px width — no horizontal scroll, no clipped/overlapping content
- [ ] 768px, 1024px — layout transitions make sense, not just "same layout squished"
- [ ] 200% zoom — content readable, nothing cut off
- [ ] Touch targets ≥ 40-44px on mobile viewport
- [ ] Landscape orientation on mobile — layout doesn't break
- [ ] Dark mode — check contrast still holds in dark theme, not just light