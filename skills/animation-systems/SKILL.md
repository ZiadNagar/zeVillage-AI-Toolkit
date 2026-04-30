---
name: animation-systems
description: "Use this skill when designing a motion design system — defining duration scales, easing tokens, choreography patterns, performance budgets, and reduced-motion support for product UI."
license: Complete terms in LICENSE.txt
---

# Animation Systems

## When to Use

- Establishing a consistent motion language across a product (Stripe / Linear / Apple / Vercel style).
- Defining reusable duration, easing, and choreography tokens for a design system.
- Auditing an existing codebase for motion consistency and performance.
- Adding `prefers-reduced-motion` support to an application.
- Onboarding a team to shared animation conventions.

## Key Concepts

### Motion Primitives

A motion primitive is the smallest reusable animation building block: a named combination of **property**, **duration**, **easing**, and optional **delay**. Example primitives:

| Primitive   | Property              | Duration | Easing      |
| ----------- | --------------------- | -------- | ----------- |
| `fade-in`   | `opacity 0 → 1`       | 200 ms   | ease-out    |
| `slide-up`  | `translateY 12px → 0` | 250 ms   | ease-out    |
| `scale-pop` | `scale 0.95 → 1`      | 200 ms   | ease-out    |
| `collapse`  | `height auto → 0`     | 300 ms   | ease-in-out |

Compose primitives to build higher-level patterns (e.g., a modal entrance = `fade-in` + `scale-pop`).

### Duration Scale

Use a finite set of named durations so every animation feels part of the same system.

| Token                      | Range      | Use Case                                           |
| -------------------------- | ---------- | -------------------------------------------------- |
| `--motion-duration-micro`  | 100–150 ms | Button press feedback, toggle, checkbox.           |
| `--motion-duration-small`  | 200–300 ms | Tooltips, dropdowns, small reveals.                |
| `--motion-duration-medium` | 300–500 ms | Modals, page-section transitions, cards.           |
| `--motion-duration-large`  | 500 ms+    | Full-page transitions, hero entrances, onboarding. |

> **Rule of thumb:** if the user initiated the action, use a shorter duration. If the system initiates (e.g., a notification appearing), a slightly longer duration feels less abrupt.

### Easing Tokens

Map each easing to its semantic role:

| Token           | CSS Value                           | When to Use                                                    |
| --------------- | ----------------------------------- | -------------------------------------------------------------- |
| `--ease-out`    | `cubic-bezier(0.16, 1, 0.3, 1)`     | **Entrances** — element decelerates into its resting position. |
| `--ease-in`     | `cubic-bezier(0.7, 0, 1, 1)`        | **Exits** — element accelerates out of view.                   |
| `--ease-in-out` | `cubic-bezier(0.65, 0, 0.35, 1)`    | **State changes** — element morphs in place (resize, reorder). |
| `--ease-linear` | `linear`                            | **Progress indicators**, looping rotations.                    |
| `--ease-spring` | `cubic-bezier(0.34, 1.56, 0.64, 1)` | **Playful UI** — slight overshoot for delight (use sparingly). |

### Choreography Patterns

Choreography turns individual animations into a coordinated sequence.

**Stagger children.** When a group appears (list, grid, cards), offset each item by a small delay (40–80 ms). Keep total stagger under ~400 ms so the group still feels unified.

```css
.card-list > * {
  animation: fade-slide-up var(--motion-duration-small) var(--ease-out) both;
}
.card-list > *:nth-child(1) {
  animation-delay: 0ms;
}
.card-list > *:nth-child(2) {
  animation-delay: 60ms;
}
.card-list > *:nth-child(3) {
  animation-delay: 120ms;
}
/* … use a loop or CSS custom property in practice */
```

**Sequence related elements.** Animate the container first (e.g., background fade), then its content (heading → body → CTA). Overlap slightly so it feels fluid, not robotic.

**Shared-element transitions.** When navigating between views, morph common elements (thumbnail → hero image) with `view-transition-name` or a FLIP technique (`getBoundingClientRect` before → animate to → after).

### Performance Rules

1. **Only animate composited properties:** `transform` (`translate`, `scale`, `rotate`) and `opacity`. These skip layout and paint.
2. **Avoid animating `width`, `height`, `top`, `left`, `margin`, `padding`** — they trigger expensive layout recalculations.
3. **Use `will-change` sparingly.** Apply it just before an animation starts and remove it after. Blanket `will-change: transform` wastes GPU memory.
4. **Cap total concurrent animations.** More than ~12 simultaneous CSS animations on-screen can cause frame drops on low-end devices.
5. **Test on real devices.** Desktop DevTools throttling is a rough proxy — actual mobile GPUs and CPUs behave differently.

### Reduced Motion Support

Always respect `prefers-reduced-motion`:

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

- Do **not** remove animations entirely — keep opacity fades so state changes remain visible.
- Replace sliding/scaling with simple cross-fades.
- Disable parallax and scroll-jacking completely.
- In JS, check `window.matchMedia("(prefers-reduced-motion: reduce)").matches` before starting animations.

## Quick Recipes

### CSS Custom Properties Motion Tokens

```css
:root {
  /* Durations */
  --motion-duration-micro: 120ms;
  --motion-duration-small: 220ms;
  --motion-duration-medium: 350ms;
  --motion-duration-large: 600ms;

  /* Easings */
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
  --ease-in: cubic-bezier(0.7, 0, 1, 1);
  --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1);
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1);
}
```

### Reusable Fade-Slide-Up Keyframe

```css
@keyframes fade-slide-up {
  from {
    opacity: 0;
    transform: translateY(12px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-enter {
  animation: fade-slide-up var(--motion-duration-small) var(--ease-out) both;
}
```

### Stagger with CSS Custom Properties

```css
.stagger-list > * {
  --i: 0;
  animation: fade-slide-up var(--motion-duration-small) var(--ease-out) both;
  animation-delay: calc(var(--i) * 60ms);
}
.stagger-list > *:nth-child(1) {
  --i: 0;
}
.stagger-list > *:nth-child(2) {
  --i: 1;
}
.stagger-list > *:nth-child(3) {
  --i: 2;
}
.stagger-list > *:nth-child(4) {
  --i: 3;
}
```

### JS Motion Token Object

```js
export const motion = {
  duration: {
    micro: 120,
    small: 220,
    medium: 350,
    large: 600,
  },
  ease: {
    out: [0.16, 1, 0.3, 1],
    in: [0.7, 0, 1, 1],
    inOut: [0.65, 0, 0.35, 1],
    spring: [0.34, 1.56, 0.64, 1],
  },
  stagger: {
    default: 60, // ms between children
    fast: 40,
    slow: 80,
  },
};
```

### React Reduced-Motion Hook

```jsx
import { useState, useEffect } from "react";

export function usePrefersReducedMotion() {
  const [reduced, setReduced] = useState(false);

  useEffect(() => {
    const mql = window.matchMedia("(prefers-reduced-motion: reduce)");
    setReduced(mql.matches);
    const handler = (e) => setReduced(e.matches);
    mql.addEventListener("change", handler);
    return () => mql.removeEventListener("change", handler);
  }, []);

  return reduced;
}
```

## Common Pitfalls

| Pitfall                           | Fix                                                                                                                                        |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **Inconsistent durations**        | Define a token scale and lint against raw `ms` values in CSS / JS.                                                                         |
| **Using `ease-in` for entrances** | Elements feel sluggish appearing. Use `ease-out` for entrances, `ease-in` only for exits.                                                  |
| **Stagger too long**              | Total stagger > 400 ms makes the group feel slow. Cap it or animate the container instead.                                                 |
| **Ignoring reduced motion**       | Always provide a `prefers-reduced-motion` fallback. Many users enable it; some organizations require it for accessibility compliance.      |
| **Animating layout properties**   | `height`, `width`, `top`, `left` cause reflow. Use `transform` and `opacity` instead.                                                      |
| **Over-animating**                | Not every state change needs motion. Reserve animation for functional feedback (confirms action, guides attention) rather than decoration. |

## Best Practices

1. **Codify tokens, not one-off values.** Treat durations and easings like color or spacing tokens — define once, reference everywhere.
2. **Match duration to travel distance.** Small UI elements use micro/small durations; full-page transitions use medium/large.
3. **Entrance ease-out, exit ease-in, morph ease-in-out.** Memorize this rule and deviations will immediately feel intentional.
4. **Start without animation, then add it.** A functional UI that works at `duration: 0` is the baseline. Motion enhances — it should never be required to understand state.
5. **Choreograph parent before children.** Fade in the container, then stagger its children. This gives the eye an anchor.
6. **Audit with slow-motion.** Use DevTools → Animations → 25 % speed to catch overlapping or competing animations.
7. **Document your motion system.** Record tokens, primitives, and choreography rules in a shared design-system page so designers and engineers stay aligned.
8. **Test on low-end devices.** If frame drops appear, simplify — reduce concurrency, shorten durations, or remove non-essential animations.
