---
name: css-alpha-masking
description: Use this skill when creating content fade effects, vignettes, or scroll indicators using CSS mask-image with linear-gradient alpha masking.
license: Complete terms in LICENSE.txt
---

# CSS Alpha Masking

Use `mask-image` with `linear-gradient` to control element opacity through gradient-based alpha masks — fading content at edges, creating vignettes, and building scroll indicators.

## When to Use

- Fading text or images at the edges of a container (horizontal or vertical)
- Creating vignette effects on images or video
- Indicating scrollable content with fade-out hints at list boundaries
- Masking arbitrary content to reveal or hide portions with smooth transitions
- Combining multiple masks for complex reveal patterns

## Key Concepts

### Core Technique

The CSS `mask-image` property uses the alpha channel of an image (or gradient) to determine which parts of an element are visible:

- **Black** (`rgb(0,0,0)` or `black`) = fully visible
- **Transparent** (`transparent` or `rgba(0,0,0,0)`) = fully hidden
- Values in between = partially visible

A `linear-gradient` from `transparent` to `black` creates a smooth fade.

### Horizontal Fade

Fade content at the left and right edges:

```css
mask-image: linear-gradient(
  to right,
  transparent,
  black 20%,
  black 80%,
  transparent
);
-webkit-mask-image: linear-gradient(
  to right,
  transparent,
  black 20%,
  black 80%,
  transparent
);
```

This keeps the center 60% fully visible while fading the outer 20% on each side.

### Vertical Fade

Fade content at the top and bottom edges:

```css
mask-image: linear-gradient(
  to bottom,
  transparent,
  black 20%,
  black 80%,
  transparent
);
-webkit-mask-image: linear-gradient(
  to bottom,
  transparent,
  black 20%,
  black 80%,
  transparent
);
```

### Browser Support

The `-webkit-mask-image` prefix is still required for Safari and some WebKit-based browsers. Always include both the prefixed and unprefixed properties:

```css
mask-image: /* value */;
-webkit-mask-image: /* value */;
```

Similarly for related properties: `mask-composite` / `-webkit-mask-composite`, `mask-size` / `-webkit-mask-size`, `mask-repeat` / `-webkit-mask-repeat`.

## Quick Recipes

### Text Fade-Out at Edges

Fade long horizontal text at both sides, useful for single-line tickers or overflow previews.

```html
<div class="text-fade-horizontal">
  <p>
    This is a long line of text that fades out smoothly at the left and right
    edges of its container to indicate overflow.
  </p>
</div>
```

```css
/* ── Horizontal text fade ──────────────────────── */
.text-fade-horizontal {
  overflow: hidden;
  white-space: nowrap;
  max-width: 400px;

  mask-image: linear-gradient(
    to right,
    transparent,
    black 10%,
    black 90%,
    transparent
  );
  -webkit-mask-image: linear-gradient(
    to right,
    transparent,
    black 10%,
    black 90%,
    transparent
  );
}
```

### Vertical Text Fade (Read-More Preview)

Show a truncated block of text that fades to transparent at the bottom.

```html
<div class="text-fade-bottom">
  <p>
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod
    tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
    quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
    consequat. Duis aute irure dolor in reprehenderit.
  </p>
</div>
```

```css
/* ── Bottom text fade (read-more) ──────────────── */
.text-fade-bottom {
  max-height: 120px;
  overflow: hidden;
  position: relative;

  mask-image: linear-gradient(to bottom, black 0%, black 50%, transparent 100%);
  -webkit-mask-image: linear-gradient(
    to bottom,
    black 0%,
    black 50%,
    transparent 100%
  );
}
```

### Image Vignette

Apply a radial fade around an image to create a vignette effect.

```html
<img src="photo.jpg" alt="Photo" class="vignette" />
```

```css
/* ── Image vignette via radial mask ────────────── */
.vignette {
  display: block;
  width: 100%;
  max-width: 600px;

  mask-image: radial-gradient(
    ellipse 70% 70% at center,
    black 60%,
    transparent 100%
  );
  -webkit-mask-image: radial-gradient(
    ellipse 70% 70% at center,
    black 60%,
    transparent 100%
  );
}
```

### Scrollable List with Fade Indicators

Indicate to the user that a list scrolls vertically by fading the top and bottom edges.

```html
<ul class="scroll-fade-list">
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
  <li>Item 4</li>
  <li>Item 5</li>
  <li>Item 6</li>
  <li>Item 7</li>
  <li>Item 8</li>
  <li>Item 9</li>
  <li>Item 10</li>
</ul>
```

```css
/* ── Scrollable list with fade edges ───────────── */
.scroll-fade-list {
  max-height: 200px;
  overflow-y: auto;
  list-style: none;
  padding: 0;
  margin: 0;

  mask-image: linear-gradient(
    to bottom,
    transparent,
    black 15%,
    black 85%,
    transparent
  );
  -webkit-mask-image: linear-gradient(
    to bottom,
    transparent,
    black 15%,
    black 85%,
    transparent
  );
}

.scroll-fade-list li {
  padding: 12px 16px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  color: #e0e0e0;
}
```

### Diagonal Fade

Mask content along a diagonal axis.

```css
/* ── Diagonal fade ─────────────────────────────── */
.diagonal-fade {
  mask-image: linear-gradient(135deg, black 30%, transparent 70%);
  -webkit-mask-image: linear-gradient(135deg, black 30%, transparent 70%);
}
```

### Combining Multiple Masks

Layer two masks to create a combined fade effect — for example, fading both horizontally and vertically.

```css
/* ── Combined horizontal + vertical fade ───────── */
.combined-fade {
  mask-image:
    linear-gradient(to right, transparent, black 15%, black 85%, transparent),
    linear-gradient(to bottom, transparent, black 15%, black 85%, transparent);
  -webkit-mask-image:
    linear-gradient(to right, transparent, black 15%, black 85%, transparent),
    linear-gradient(to bottom, transparent, black 15%, black 85%, transparent);

  /* Intersect both masks so only the center area is visible */
  mask-composite: intersect;
  -webkit-mask-composite: source-in;
}
```

### Dynamic Fade with CSS Custom Properties

Make fade distances adjustable via custom properties.

```css
/* ── Custom property-driven fade ───────────────── */
.dynamic-fade {
  --fade-start: 10%;
  --fade-end: 90%;

  mask-image: linear-gradient(
    to bottom,
    transparent,
    black var(--fade-start),
    black var(--fade-end),
    transparent
  );
  -webkit-mask-image: linear-gradient(
    to bottom,
    transparent,
    black var(--fade-start),
    black var(--fade-end),
    transparent
  );
}

/* Adjust from JavaScript or media queries */
@media (max-width: 768px) {
  .dynamic-fade {
    --fade-start: 5%;
    --fade-end: 95%;
  }
}
```

## Common Pitfalls

1. **Missing `-webkit-mask-image` prefix** — Safari and older WebKit browsers require the prefixed property. Without it the mask is ignored entirely, showing the full unmasked content.

2. **Using `white` instead of `black`** — CSS masks use the alpha channel, not luminance, when applied with `mask-image`. Use `black` (fully opaque) for visible areas and `transparent` (zero alpha) for hidden areas. Using `white` works the same as `black` here since both are fully opaque, but `transparent` vs. `black` is what matters.

3. **Mask on replaced elements** — `mask-image` may behave unexpectedly on `<video>` or `<iframe>` in some browsers. Wrap the element in a `<div>` and apply the mask to the wrapper.

4. **Scrollbar clipping** — When applying a mask to a scrollable container, the scrollbar itself may be masked and become invisible. Consider using a wrapper div with the mask and letting the inner element scroll.

5. **`mask-composite` prefix differences** — Standard CSS uses `mask-composite: intersect | exclude | add | subtract`. WebKit uses `-webkit-mask-composite: source-in | xor | source-over | source-out`. Map between them:
   | Standard | WebKit |
   |---|---|
   | `intersect` | `source-in` |
   | `exclude` | `xor` |
   | `add` | `source-over` |
   | `subtract` | `source-out` |

6. **Interaction with `overflow: hidden`** — Masks and `overflow: hidden` both clip content. They combine multiplicatively — an element hidden by overflow will remain hidden regardless of the mask.

7. **Performance on large elements** — Complex masks with multiple gradients can trigger repaints. Keep mask gradients simple and avoid animating mask properties on large areas.

## Best Practices

- **Always include both prefixed and unprefixed** properties for `mask-image`, `mask-composite`, `mask-size`, and `mask-repeat`.
- **Use CSS custom properties** for fade distances (`--fade-start`, `--fade-end`) so they can be tuned per breakpoint or component instance.
- **Keep gradients simple** — a two or four-stop `linear-gradient` covers the vast majority of fade effects. Avoid overly complex gradients unless needed.
- **Test across browsers** — mask behavior is one of the areas where Safari, Chrome, and Firefox can differ. Verify the visual result on all target browsers.
- **Combine with `overflow: hidden`** on the container to ensure no content leaks outside the masked area.
- **Use `radial-gradient`** for vignette or spotlight effects, and `linear-gradient` for edge fades.
- **For scroll indicators**, consider toggling mask classes based on scroll position via JavaScript — show a top fade only when scrolled down, bottom fade only when not at the end.
- **Document the mask purpose** in a comment. Masks are invisible in DevTools layout views and can confuse developers who don't expect content clipping.
- **Avoid animating `mask-image`** directly — it is not smoothly animatable in most browsers. Instead, animate the element's `opacity` or a CSS custom property used in the gradient stops (with `@property` registration).
