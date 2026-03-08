---
name: css-border-gradient
description: Use this skill when creating gradient borders on elements using the pseudo-element mask technique or border-image approach in CSS.
license: Complete terms in LICENSE.txt
---

# CSS Border Gradient

Techniques for applying gradient colors to element borders using pseudo-element masking or the `border-image` property.

## When to Use

- Adding rainbow, multi-color, or branded gradient borders to cards and containers
- Creating animated glowing border effects
- Building stylized input fields or buttons with gradient outlines
- Applying gradient borders to elements that also require `border-radius`

## Key Concepts

### Pseudo-Element Mask Technique (Recommended)

This is the most flexible approach and supports `border-radius`. The technique works by:

1. Setting the parent element to `position: relative`
2. Creating a `::before` pseudo-element with `position: absolute` that covers the element plus the desired border width
3. Applying the gradient as the pseudo-element's `background`
4. Using `mask` with two layers and `mask-composite: exclude` to punch out the center, leaving only the border visible

#### How the Mask Works

The mask uses two overlapping `linear-gradient(white, white)` layers:

- **First layer**: covers the content area (padding-box) — this is the area to cut out
- **Second layer**: covers the full area (border-box or the pseudo-element itself)

By compositing these with `mask-composite: exclude` (or `-webkit-mask-composite: xor`), the center is removed and only the border ring of the gradient remains.

### Border-Image Approach (Simple, No Border-Radius)

The `border-image` property can apply a gradient directly:

```css
border-image: linear-gradient(to right, #ff0080, #7928ca) 1;
```

This is simpler but **does not work with `border-radius`** — the corners will remain square.

## Quick Recipes

### Rainbow Border Card

A card with a smooth rainbow gradient border that supports rounded corners.

```html
<div class="rainbow-border-card">
  <div class="card-content">
    <h2>Gradient Border</h2>
    <p>This card has a rainbow gradient border with rounded corners.</p>
  </div>
</div>
```

```css
/* ── Rainbow border card ───────────────────────── */
.rainbow-border-card {
  position: relative;
  border-radius: 16px;
  padding: 2px; /* This becomes the visible border width */
  background: linear-gradient(
    135deg,
    #ff0080,
    #ff8c00,
    #40e0d0,
    #7928ca,
    #ff0080
  );
}

.rainbow-border-card .card-content {
  background: #0f0f0f;
  border-radius: 14px; /* parent radius minus padding */
  padding: 24px;
  color: #ffffff;
}
```

> **Note**: This "padding as border" technique is the simplest gradient border method that supports `border-radius`. The parent carries the gradient background; the inner element covers the center with a solid background.

### Gradient Border with Mask Technique

The full pseudo-element mask approach for cases where the inner background must be transparent or complex.

```html
<div class="gradient-border-mask">
  <h2>Masked Gradient Border</h2>
  <p>Uses mask-composite to show only the border.</p>
</div>
```

```css
/* ── Gradient border via mask ──────────────────── */
.gradient-border-mask {
  --border-width: 2px;
  --border-radius: 16px;

  position: relative;
  border-radius: var(--border-radius);
  padding: 24px;
  color: #ffffff;
  background: #1a1a2e;
}

.gradient-border-mask::before {
  content: "";
  position: absolute;
  inset: calc(-1 * var(--border-width));
  border-radius: calc(var(--border-radius) + var(--border-width));
  background: linear-gradient(135deg, #667eea, #764ba2);
  z-index: -1;

  /* Mask: cut out the center to leave only the border */
  mask:
    linear-gradient(#fff 0 0) content-box,
    linear-gradient(#fff 0 0);
  mask-composite: exclude;

  -webkit-mask:
    linear-gradient(#fff 0 0) content-box,
    linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;

  padding: var(--border-width);
}
```

### Animated Gradient Border

A rotating gradient border using `@property` for hue animation.

```html
<div class="animated-gradient-border">
  <div class="card-inner">
    <h2>Animated Border</h2>
    <p>The gradient rotates around the border continuously.</p>
  </div>
</div>
```

```css
/* ── Animated gradient border ──────────────────── */
@property --gradient-angle {
  syntax: "<angle>";
  initial-value: 0deg;
  inherits: false;
}

.animated-gradient-border {
  position: relative;
  border-radius: 16px;
  padding: 2px;
  background: conic-gradient(
    from var(--gradient-angle),
    #ff0080,
    #ff8c00,
    #40e0d0,
    #7928ca,
    #ff0080
  );
  animation: border-rotate 3s linear infinite;
}

.animated-gradient-border .card-inner {
  background: #0f0f0f;
  border-radius: 14px;
  padding: 24px;
  color: #fff;
}

@keyframes border-rotate {
  to {
    --gradient-angle: 360deg;
  }
}
```

### Subtle Gradient Border

A soft, low-contrast gradient for understated UI elements.

```css
/* ── Subtle gradient border ────────────────────── */
.subtle-gradient-border {
  position: relative;
  border-radius: 12px;
  padding: 1px;
  background: linear-gradient(
    180deg,
    rgba(255, 255, 255, 0.15),
    rgba(255, 255, 255, 0.05)
  );
}

.subtle-gradient-border .inner {
  background: #1c1c1e;
  border-radius: 11px;
  padding: 20px;
  color: #e0e0e0;
}
```

### Border-Image Approach (No Border-Radius)

The simplest method, but incompatible with rounded corners.

```css
/* ── border-image gradient (square corners only) ─ */
.border-image-gradient {
  border: 3px solid;
  border-image: linear-gradient(to right, #12c2e9, #c471ed, #f64f59) 1;
  padding: 24px;
  color: #ffffff;
  background: #111;
}
```

## Common Pitfalls

1. **`mask-composite` browser prefixes** — Standard CSS uses `mask-composite: exclude` while WebKit browsers require `-webkit-mask-composite: xor`. Always include both:

   ```css
   mask-composite: exclude;
   -webkit-mask-composite: xor;
   ```

2. **Forgetting `position: relative`** — The pseudo-element is absolutely positioned—without a relative parent, it will escape the element's bounds.

3. **Border-radius mismatch** — When using the padding technique, the inner element's `border-radius` must be the parent's radius minus the padding. Otherwise the inner corners will peek out or not align.

4. **`z-index: -1` on pseudo-element** — The `::before` gradient layer should sit behind the content. If the parent has a `z-index` or stacking context that prevents this, restructure with an explicit inner wrapper.

5. **`border-image` kills `border-radius`** — The `border-image` property overrides `border-radius` entirely. If rounded corners are needed, use the padding or mask technique instead.

6. **Transparent backgrounds** — If the element's background is transparent, the padding approach will show the gradient through the center. Use the mask technique instead.

7. **`@property` support** — The `@property` rule for animating CSS custom properties is not supported in Firefox (as of early 2025). Provide a fallback or use JavaScript-driven animation for cross-browser animated borders.

## Best Practices

- **Use the padding technique** (gradient on parent, solid background on child) as the default approach — it is the simplest and most reliable.
- **Reserve the mask technique** for cases where the inner background must be transparent, semi-transparent, or where you cannot add an inner wrapper element.
- **Keep border widths thin** (1–3px) for subtle, polished effects. Thick gradient borders can look garish.
- **Test in Safari** — verify both `-webkit-mask-composite: xor` and `-webkit-mask` shorthand behavior.
- **Use CSS custom properties** for border width and radius so the effect is easy to adjust across breakpoints.
- **Animate with `@property`** for smooth hue or angle transitions. Fall back to a static gradient when `@property` is unsupported.
- **Combine with `box-shadow`** for glow effects — add a colored `box-shadow` that matches the gradient's dominant hue for extra depth.
- **Document the technique** in code comments so other developers understand why a pseudo-element or inner wrapper is used instead of a plain `border`.
