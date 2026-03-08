---
name: progressive-blur
description: Use this skill when creating smooth progressive blur effects on images or sections using layered backdrop-filter with CSS mask-image gradients.
license: Complete terms in LICENSE.txt
---

# Progressive Blur

A CSS technique for creating smooth, gradual blur transitions using multiple stacked pseudo-layers with increasing `backdrop-filter: blur()` values and `mask-image` linear gradients.

## When to Use

- Fading a hero image into a blurred background at the top or bottom edge
- Creating frosted-glass headers or footers that blend smoothly into content
- Adding depth-of-field style blur transitions to sections
- Building scroll-aware blur overlays on media-heavy layouts

## Key Concepts

### Layered Approach

A single `backdrop-filter: blur()` applies uniformly. To create a **progressive** (gradual) blur, stack multiple absolutely-positioned pseudo-layers inside a container. Each layer:

1. Covers the full area (`inset: 0`)
2. Applies a successively stronger `blur()` value
3. Uses `mask-image: linear-gradient(...)` so it is only visible in its designated band of the gradient

The result is a seamless ramp from sharp to blurred across the container.

### How the Gradient Mask Works

Each layer's `mask-image` gradient defines **where** that layer is opaque. By offsetting the gradient stops for each layer, you carve the container into bands. Layer 1 handles the first band (lightest blur), layer 2 the next band (stronger blur), and so on up to the final layer (maximum blur).

### Customization Knobs

| Knob               | What it controls                               | Default |
| ------------------ | ---------------------------------------------- | ------- |
| `--blur-max`       | Maximum blur on the last layer                 | `12px`  |
| `--blur-layers`    | Number of pseudo-layers (more = smoother)      | `8`     |
| `--blur-height`    | Height of the blur region                      | `300px` |
| Gradient direction | `to bottom` (top-down) or `to top` (bottom-up) | varies  |

## Quick Recipes

### Bottom Blur — Hero Image Fading to Blur at the Bottom

Use this when a hero image should gradually blur toward the bottom edge.

```html
<div class="hero-blur-bottom">
  <img src="hero.jpg" alt="Hero" class="hero-image" />
</div>
```

```css
/* ── Container ─────────────────────────────────── */
.hero-blur-bottom {
  position: relative;
  width: 100%;
  height: 500px;
  overflow: hidden;
}

.hero-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

/* ── Blur overlay container ────────────────────── */
.hero-blur-bottom::after {
  content: "";
  position: absolute;
  inset: 0;
  pointer-events: none;
}

/* ── Progressive blur layers (bottom blur) ─────── */
.hero-blur-bottom .blur-region {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: var(--blur-height, 300px);
  pointer-events: none;
}

/* Layer 1 */
.hero-blur-bottom .blur-region .blur-layer-1 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(1.5px);
  -webkit-backdrop-filter: blur(1.5px);
  mask-image: linear-gradient(to bottom, transparent 0%, black 12.5%);
  -webkit-mask-image: linear-gradient(to bottom, transparent 0%, black 12.5%);
}

/* Layer 2 */
.hero-blur-bottom .blur-region .blur-layer-2 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(3px);
  -webkit-backdrop-filter: blur(3px);
  mask-image: linear-gradient(to bottom, transparent 12.5%, black 25%);
  -webkit-mask-image: linear-gradient(to bottom, transparent 12.5%, black 25%);
}

/* Layer 3 */
.hero-blur-bottom .blur-region .blur-layer-3 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(4.5px);
  -webkit-backdrop-filter: blur(4.5px);
  mask-image: linear-gradient(to bottom, transparent 25%, black 37.5%);
  -webkit-mask-image: linear-gradient(to bottom, transparent 25%, black 37.5%);
}

/* Layer 4 */
.hero-blur-bottom .blur-region .blur-layer-4 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(6px);
  -webkit-backdrop-filter: blur(6px);
  mask-image: linear-gradient(to bottom, transparent 37.5%, black 50%);
  -webkit-mask-image: linear-gradient(to bottom, transparent 37.5%, black 50%);
}

/* Layer 5 */
.hero-blur-bottom .blur-region .blur-layer-5 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(7.5px);
  -webkit-backdrop-filter: blur(7.5px);
  mask-image: linear-gradient(to bottom, transparent 50%, black 62.5%);
  -webkit-mask-image: linear-gradient(to bottom, transparent 50%, black 62.5%);
}

/* Layer 6 */
.hero-blur-bottom .blur-region .blur-layer-6 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(9px);
  -webkit-backdrop-filter: blur(9px);
  mask-image: linear-gradient(to bottom, transparent 62.5%, black 75%);
  -webkit-mask-image: linear-gradient(to bottom, transparent 62.5%, black 75%);
}

/* Layer 7 */
.hero-blur-bottom .blur-region .blur-layer-7 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(10.5px);
  -webkit-backdrop-filter: blur(10.5px);
  mask-image: linear-gradient(to bottom, transparent 75%, black 87.5%);
  -webkit-mask-image: linear-gradient(to bottom, transparent 75%, black 87.5%);
}

/* Layer 8 */
.hero-blur-bottom .blur-region .blur-layer-8 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  mask-image: linear-gradient(to bottom, transparent 87.5%, black 100%);
  -webkit-mask-image: linear-gradient(to bottom, transparent 87.5%, black 100%);
}
```

```html
<!-- Full markup for the bottom-blur hero -->
<div class="hero-blur-bottom">
  <img src="hero.jpg" alt="Hero" class="hero-image" />
  <div class="blur-region">
    <div class="blur-layer-1"></div>
    <div class="blur-layer-2"></div>
    <div class="blur-layer-3"></div>
    <div class="blur-layer-4"></div>
    <div class="blur-layer-5"></div>
    <div class="blur-layer-6"></div>
    <div class="blur-layer-7"></div>
    <div class="blur-layer-8"></div>
  </div>
</div>
```

### Top Blur — Content Fading to Blur at the Top

Flip the gradient direction to blur from the top edge downward.

```css
/* ── Progressive blur layers (top blur) ────────── */
.top-blur-region {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: var(--blur-height, 300px);
  pointer-events: none;
}

/* Layer 1 */
.top-blur-region .blur-layer-1 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  mask-image: linear-gradient(to bottom, black 0%, transparent 12.5%);
  -webkit-mask-image: linear-gradient(to bottom, black 0%, transparent 12.5%);
}

/* Layer 2 */
.top-blur-region .blur-layer-2 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(10.5px);
  -webkit-backdrop-filter: blur(10.5px);
  mask-image: linear-gradient(to bottom, black 12.5%, transparent 25%);
  -webkit-mask-image: linear-gradient(to bottom, black 12.5%, transparent 25%);
}

/* Layer 3 */
.top-blur-region .blur-layer-3 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(9px);
  -webkit-backdrop-filter: blur(9px);
  mask-image: linear-gradient(to bottom, black 25%, transparent 37.5%);
  -webkit-mask-image: linear-gradient(to bottom, black 25%, transparent 37.5%);
}

/* Layer 4 */
.top-blur-region .blur-layer-4 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(7.5px);
  -webkit-backdrop-filter: blur(7.5px);
  mask-image: linear-gradient(to bottom, black 37.5%, transparent 50%);
  -webkit-mask-image: linear-gradient(to bottom, black 37.5%, transparent 50%);
}

/* Layer 5 */
.top-blur-region .blur-layer-5 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(6px);
  -webkit-backdrop-filter: blur(6px);
  mask-image: linear-gradient(to bottom, black 50%, transparent 62.5%);
  -webkit-mask-image: linear-gradient(to bottom, black 50%, transparent 62.5%);
}

/* Layer 6 */
.top-blur-region .blur-layer-6 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(4.5px);
  -webkit-backdrop-filter: blur(4.5px);
  mask-image: linear-gradient(to bottom, black 62.5%, transparent 75%);
  -webkit-mask-image: linear-gradient(to bottom, black 62.5%, transparent 75%);
}

/* Layer 7 */
.top-blur-region .blur-layer-7 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(3px);
  -webkit-backdrop-filter: blur(3px);
  mask-image: linear-gradient(to bottom, black 75%, transparent 87.5%);
  -webkit-mask-image: linear-gradient(to bottom, black 75%, transparent 87.5%);
}

/* Layer 8 */
.top-blur-region .blur-layer-8 {
  position: absolute;
  inset: 0;
  backdrop-filter: blur(1.5px);
  -webkit-backdrop-filter: blur(1.5px);
  mask-image: linear-gradient(to bottom, black 87.5%, transparent 100%);
  -webkit-mask-image: linear-gradient(to bottom, black 87.5%, transparent 100%);
}
```

### Compact Utility — CSS Custom Properties Driven

A more maintainable version using CSS custom properties for each layer. The AI agent can generate these layers programmatically.

```css
.progressive-blur {
  --blur-layers: 8;
  --blur-max: 12px;
  position: relative;
}

.progressive-blur .blur-layer {
  position: absolute;
  inset: 0;
  pointer-events: none;
}

/* Each layer sets its own --i (0-based index) */
.progressive-blur .blur-layer[data-layer="0"] {
  backdrop-filter: blur(1.5px);
  -webkit-backdrop-filter: blur(1.5px);
  mask-image: linear-gradient(to bottom, transparent 0%, black 12.5%);
  -webkit-mask-image: linear-gradient(to bottom, transparent 0%, black 12.5%);
}

.progressive-blur .blur-layer[data-layer="7"] {
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  mask-image: linear-gradient(to bottom, transparent 87.5%, black 100%);
  -webkit-mask-image: linear-gradient(to bottom, transparent 87.5%, black 100%);
}
```

The AI agent should generate all intermediate layers following the pattern: `blur = (index + 1) / layers * max_blur` and gradient stops at `index / layers * 100%` to `(index + 1) / layers * 100%`.

## Common Pitfalls

1. **Missing `-webkit-` prefix** — `backdrop-filter` and `mask-image` both require `-webkit-` prefixes for Safari and older Chrome. Always include both the prefixed and unprefixed properties.

2. **Container needs `position: relative`** — The blur layers use `position: absolute`, so the parent must establish a positioning context.

3. **Pointer events blocking interaction** — Always set `pointer-events: none` on blur layers so clicks pass through to the content underneath.

4. **Too few layers** — Using fewer than 4 layers produces visible banding. Eight layers give a smooth result for most use cases.

5. **Performance on large areas** — Each `backdrop-filter` layer triggers compositing. On very large containers or low-powered devices, reduce the layer count or limit the blur region height.

6. **Overflow hidden required** — The container should have `overflow: hidden` to prevent blur layers from extending beyond its bounds.

7. **Z-index conflicts** — Blur layers should sit above the content being blurred but below any overlaid text or UI. Use explicit `z-index` ordering when needed.

## Best Practices

- **Start with 8 layers** for a smooth gradient, then reduce if performance is a concern.
- **Keep blur region height proportional** — a blur height of 40–60% of the container works well visually.
- **Use CSS custom properties** (`--blur-max`, `--blur-height`) so designs are easy to tune without editing every layer.
- **Test on Safari** — always verify with `-webkit-backdrop-filter` and `-webkit-mask-image`.
- **Combine with a semi-transparent overlay** if you need to tint the blurred region (e.g., a dark gradient overlay for white text on a hero).
- **Prefer `backdrop-filter`** over duplicating and blurring the background image — it works on dynamic content and avoids layout duplication.
- **Document the layer formula** in a comment so future maintainers understand the pattern: blur increment = `max_blur / layer_count`, gradient band = `100% / layer_count`.
