# Contrast Formulas

## WCAG 2.x contrast ratio (the enforceable legal standard — use this as primary citation)

Formula: `(L1 + 0.05) / (L2 + 0.05)` where L1 = lighter relative luminance, L2 = darker.

Relative luminance per channel (sRGB):
```js
function luminance(r, g, b) {
  const [rs, gs, bs] = [r, g, b].map(c => {
    c /= 255;
    return c <= 0.03928 ? c / 12.92 : Math.pow((c + 0.055) / 1.055, 2.4);
  });
  return 0.2126 * rs + 0.7152 * gs + 0.0722 * bs;
}

function contrastRatio(rgb1, rgb2) {
  const L1 = luminance(...rgb1);
  const L2 = luminance(...rgb2);
  const [light, dark] = L1 > L2 ? [L1, L2] : [L2, L1];
  return (light + 0.05) / (dark + 0.05);
}
```

Thresholds:
| Content type | AA | AAA |
|---|---|---|
| Normal text | 4.5:1 | 7:1 |
| Large text (≥24px, or ≥19px bold) | 3:1 | 4.5:1 |
| UI components / graphical objects | 3:1 | — |

## APCA (Advanced Perceptual Contrast Algorithm)

Perceptually more accurate than WCAG 2.x ratio — accounts for font weight/size, direction
(dark-on-light vs light-on-dark read differently at same ratio). Slated for WCAG 3, **not yet
a legally enforceable standard** — cite as supplementary insight, not compliance proof.

Rough guidance (Lc = APCA contrast value, 0-106+ scale):
- Body text: Lc 75+ recommended
- Large/bold text: Lc 60+ acceptable
- Placeholder/disabled text: Lc 45+ minimum

When reviewing: report WCAG ratio as the compliance number, optionally note "APCA suggests this
may read as [lower/higher] contrast than the ratio implies" if font is unusually thin/thick.

## Tools to reference in review output

- Chrome DevTools → Elements → color picker shows live contrast ratio inline
- Stark (Figma/Sketch/browser plugin)
- WebAIM Contrast Checker (webaim.org/resources/contrastchecker) — quick manual spot check
- `axe-core` — flags contrast failures programmatically in CI

## Common failure patterns to flag

- Light gray text on white (`#999` on `#fff` ≈ 2.8:1 — fails AA)
- Placeholder text used as the only label (double failure: contrast + missing label SC 3.3.2)
- Brand color used for body text without checking ratio against background
- Disabled-state buttons that are actually meant to be readable (vs decoratively muted)
- Gradient backgrounds — check contrast at the worst point of the gradient, not average