---
name: animejs
description: "Use this skill when building web animations with Anime.js v4 — tweens, timelines, stagger effects, scroll-driven animation, SVG line drawing, and draggable interactions."
license: Complete terms in LICENSE.txt
---

# Anime.js v4

## When to Use

- Animating DOM elements, SVG attributes, or JS objects with a lightweight library.
- Building sequenced timelines with precise overlap control.
- Creating staggered grid or list animations.
- Drawing SVG paths (line-drawing effect).
- Adding scroll-triggered animations without a heavy framework.
- Needing draggable elements with physics-style inertia.

## Key Concepts

### Installation

```bash
npm install animejs
```

### Module Imports

Anime.js v4 is fully tree-shakeable. Import only what you need:

```js
import { animate, stagger, createTimeline, createDraggable } from "animejs";
```

Full bundle: ~24.5 KB minified. Tree-shaking reduces this significantly.

### Core API

| Function                           | Purpose                                        |
| ---------------------------------- | ---------------------------------------------- |
| `animate(targets, params)`         | Animate one or more targets.                   |
| `createTimeline(defaults)`         | Create a timeline to sequence animations.      |
| `stagger(value, options)`          | Generate staggered delays or values.           |
| `createDraggable(targets, params)` | Make elements draggable with snap and inertia. |

### Basic Animation

```js
import { animate } from "animejs";

animate(".element", {
  translateX: 250,
  rotate: "1turn",
  duration: 800,
  ease: "outExpo",
});
```

### Properties You Can Animate

- **CSS transforms:** `translateX`, `translateY`, `scale`, `rotate`, `skew`.
- **CSS properties:** `opacity`, `color`, `backgroundColor`, `borderRadius`.
- **SVG attributes:** `d`, `cx`, `cy`, `r`, `strokeDashoffset`.
- **JS object values:** Animate plain number properties on any object.

### Easing

Built-in easings follow the pattern `in`, `out`, `inOut` plus a curve name:

```
"outExpo"   "inOutQuad"   "outElastic(1, 0.5)"   "outBounce"
"linear"    "inOutSine"   "outBack(1.7)"          "spring(1, 80, 10, 0)"
```

### Keyframes

Animate through multiple steps:

```js
animate(".box", {
  keyframes: [
    { translateX: 100, duration: 400 },
    { translateY: 50, duration: 300 },
    { rotate: "1turn", duration: 500 },
  ],
  ease: "outQuad",
});
```

### Function-Based Values

Pass a function to generate per-target values:

```js
animate(".dot", {
  translateX: (el, i) => 50 + i * 30,
  duration: (el, i) => 600 + i * 100,
  delay: (el, i) => i * 80,
  ease: "outExpo",
});
```

## Quick Recipes

### Timeline

```js
import { createTimeline } from "animejs";

const tl = createTimeline({
  defaults: { duration: 600, ease: "outExpo" },
});

tl.add(".title", { opacity: [0, 1], translateY: [30, 0] })
  .add(".subtitle", { opacity: [0, 1], translateY: [20, 0] }, "-=400")
  .add(".cta", { scale: [0.8, 1], opacity: [0, 1] }, "-=300");
```

### Stagger

```js
import { animate, stagger } from "animejs";

animate(".card", {
  translateY: [40, 0],
  opacity: [0, 1],
  duration: 500,
  delay: stagger(80), // 80 ms between each card
  ease: "outQuad",
});
```

Grid stagger (radiates from center):

```js
animate(".grid-item", {
  scale: [0, 1],
  delay: stagger(50, { grid: [10, 10], from: "center" }),
  duration: 600,
  ease: "outElastic(1, 0.5)",
});
```

### Scroll-Driven Animation

```js
import { animate } from "animejs";

animate(".reveal", {
  translateY: [60, 0],
  opacity: [0, 1],
  duration: 800,
  ease: "outQuad",
  autoplay: false, // start paused
  onScroll: {
    target: ".reveal",
    enter: "bottom", // when element enters the viewport bottom
  },
});
```

### SVG Line Drawing

```js
import { animate } from "animejs";

const path = document.querySelector(".draw-path");
const length = path.getTotalLength();

// Set up initial state
path.style.strokeDasharray = length;
path.style.strokeDashoffset = length;

animate(path, {
  strokeDashoffset: [length, 0],
  duration: 2000,
  ease: "inOutSine",
});
```

### Draggable

```js
import { createDraggable } from "animejs";

createDraggable(".draggable", {
  container: ".bounds",
  snap: { x: 50, y: 50 }, // snap to 50px grid
  ease: "outExpo",
  releaseEase: "outElastic(1, 0.5)",
});
```

### Animating JS Object Values

```js
import { animate } from "animejs";

const progress = { value: 0 };

animate(progress, {
  value: 100,
  duration: 1500,
  ease: "linear",
  onUpdate: () => {
    document.querySelector(".counter").textContent = Math.round(progress.value);
  },
});
```

## React Integration

Use `createScope` to scope animations to a component and clean up on unmount:

```jsx
import { useRef, useEffect } from "react";
import { animate, createScope } from "animejs";

function AnimatedList({ items }) {
  const root = useRef(null);
  const scope = useRef(null);

  useEffect(() => {
    scope.current = createScope({ root: root.current }).add(() => {
      animate(".item", {
        translateY: [20, 0],
        opacity: [0, 1],
        delay: (el, i) => i * 60,
        duration: 500,
        ease: "outQuad",
      });
    });

    return () => scope.current.revert();
  }, []);

  return (
    <ul ref={root}>
      {items.map((item) => (
        <li key={item.id} className="item">
          {item.label}
        </li>
      ))}
    </ul>
  );
}
```

## Animation Controls

Every `animate()` call returns a controller:

```js
const anim = animate(".box", {
  translateX: 300,
  duration: 1000,
  autoplay: false,
});

anim.play();
anim.pause();
anim.reverse();
anim.restart();
anim.seek(500); // jump to 500 ms
```

## Common Pitfalls

| Pitfall                              | Fix                                                                                                                         |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| **Importing from the wrong package** | Use `import { animate } from "animejs"` (v4). Do not use `import anime from "animejs"` — that is the v3 default export.     |
| **Not cleaning up in SPAs**          | Use `createScope` and call `scope.revert()` on component unmount to kill active animations and restore initial values.      |
| **Animating layout properties**      | Prefer `translateX`/`translateY` over `left`/`top` for GPU-composited rendering.                                            |
| **Stagger with no `from` on grids**  | Without `from`, a grid stagger animates left-to-right only. Set `from: "center"` or `from: [row, col]` for radial effects.  |
| **Forgetting `autoplay: false`**     | Scroll-driven and manually-triggered animations should start paused — otherwise they fire immediately on creation.          |
| **Using v3 syntax in v4**            | v4 replaces `anime()` with `animate()`, `anime.timeline()` with `createTimeline()`, and `anime.stagger()` with `stagger()`. |

## Best Practices

1. **Tree-shake your imports.** Import only `animate`, `stagger`, etc. — not the entire library — to minimize bundle size.
2. **Use `createScope` in component frameworks.** It auto-tracks animations and provides a clean `revert()` method for teardown.
3. **Prefer transform properties.** `translateX`, `translateY`, `scale`, `rotate` are GPU-composited and avoid layout thrashing.
4. **Define timeline defaults** in `createTimeline({ defaults: {} })` to keep individual `.add()` calls short and readable.
5. **Use `stagger()` instead of manual delay math.** It handles grids, radial patterns, and eased distributions out of the box.
6. **Respect reduced motion:**
   ```js
   const reduced = matchMedia("(prefers-reduced-motion: reduce)").matches;
   if (reduced) return; // skip or simplify animation
   ```
7. **Store and control animation instances.** Save the return value of `animate()` to pause, reverse, or seek programmatically.
8. **Use keyframes for multi-step sequences** within a single element instead of chaining multiple `animate()` calls.
