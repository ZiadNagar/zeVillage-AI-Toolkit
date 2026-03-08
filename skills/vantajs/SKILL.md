---
name: vantajs
description: "Use this skill when adding animated 3D backgrounds to web pages with Vanta.js — setup, effect selection, configuration, cleanup, and framework integration."
license: Complete terms in LICENSE.txt
---

# Vanta.js

## When to Use

- Adding an animated WebGL/Three.js background to a hero section or full page.
- Choosing from pre-built 3D effects (birds, fog, waves, net, globe, etc.).
- Needing a quick, visually impressive background without writing custom shaders.
- Integrating animated backgrounds into React, Vue, or other SPA frameworks.

## Key Concepts

### What Is Vanta.js

Vanta.js provides ready-made animated 3D backgrounds powered by Three.js (or p5.js for some effects). Each effect is self-contained: pick one, configure colors and parameters, and attach it to a DOM element.

### Dependencies

Vanta.js requires Three.js as a peer dependency for most effects:

```bash
npm install three vanta
```

Or via CDN:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r134/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vanta@latest/dist/vanta.birds.min.js"></script>
```

### Available Effects

| Effect     | Description                                         |
| ---------- | --------------------------------------------------- |
| `BIRDS`    | Flocking birds simulation.                          |
| `FOG`      | Volumetric fog with color gradients.                |
| `WAVES`    | Animated wave surface.                              |
| `NET`      | Connected particles forming a net.                  |
| `CELLS`    | Organic cell-like blobs.                            |
| `TRUNK`    | Growing tree trunk / branches.                      |
| `TOPOLOGY` | Topographic contour lines.                          |
| `DOTS`     | Floating connected dots.                            |
| `RINGS`    | Concentric animated rings.                          |
| `HALO`     | Glowing radial halo effect.                         |
| `GLOBE`    | Dotted rotating globe.                              |
| `CLOUDS`   | Volumetric clouds (uses p5.js instead of Three.js). |

### Basic Setup

```js
import * as THREE from "three";
import BIRDS from "vanta/dist/vanta.birds.min";

const effect = BIRDS({
  el: "#hero-background",
  THREE: THREE,
  backgroundColor: 0x0a0a1a,
  color1: 0x6366f1,
  color2: 0x06b6d4,
  birdSize: 1.2,
  wingSpan: 20,
  quantity: 4,
  mouseControls: true,
  touchControls: true,
});
```

### Common Configuration Options

These options apply to most effects:

| Option                        | Type                 | Description                                              |
| ----------------------------- | -------------------- | -------------------------------------------------------- |
| `el`                          | string / HTMLElement | CSS selector or DOM element to attach the background to. |
| `THREE`                       | object               | Reference to the Three.js library.                       |
| `backgroundColor`             | hex number           | Background color (e.g., `0x0a0a1a`).                     |
| `color` / `color1` / `color2` | hex number           | Primary and secondary accent colors.                     |
| `mouseControls`               | boolean              | Enable mouse-reactive movement.                          |
| `touchControls`               | boolean              | Enable touch-reactive movement on mobile.                |
| `gyroControls`                | boolean              | Enable device gyroscope reactivity.                      |
| `minHeight`                   | number               | Minimum canvas height in pixels.                         |
| `minWidth`                    | number               | Minimum canvas width in pixels.                          |
| `scale`                       | number               | Geometry scale factor.                                   |
| `speed`                       | number               | Animation speed multiplier.                              |

### Effect-Specific Options

**BIRDS:**

```js
{ birdSize: 1, wingSpan: 20, speedLimit: 5, separation: 20, alignment: 20, cohesion: 20, quantity: 3 }
```

**WAVES:**

```js
{ waveHeight: 15, waveSpeed: 0.5, shininess: 50, zoom: 1 }
```

**NET:**

```js
{ points: 10, maxDistance: 20, spacing: 16, showDots: true }
```

**FOG:**

```js
{ highlightColor: 0xffc300, midtoneColor: 0xff1f00, lowlightColor: 0x2d00ff, baseColor: 0x242424, blurFactor: 0.6, speed: 1, zoom: 1 }
```

**GLOBE:**

```js
{ size: 1, backgroundColor: 0x0, color: 0xff6600, color2: 0x001166 }
```

## Quick Recipes

### Animated Background on a Div

```html
<div id="hero" style="width: 100%; height: 100vh; position: relative;">
  <h1 style="position: relative; z-index: 1; color: white; padding: 2rem;">
    Welcome
  </h1>
</div>

<script type="module">
  import * as THREE from "three";
  import NET from "vanta/dist/vanta.net.min";

  const effect = NET({
    el: "#hero",
    THREE: THREE,
    color: 0x6366f1,
    backgroundColor: 0x0f172a,
    points: 12,
    maxDistance: 22,
    spacing: 18,
    mouseControls: true,
    touchControls: true,
  });

  // Cleanup when no longer needed
  // effect.destroy();
</script>
```

### React Integration

```jsx
import { useEffect, useRef, useState } from "react";
import * as THREE from "three";
import FOG from "vanta/dist/vanta.fog.min";

export default function HeroBackground({ children }) {
  const containerRef = useRef(null);
  const [effect, setEffect] = useState(null);

  useEffect(() => {
    if (!containerRef.current) return;

    const vantaEffect = FOG({
      el: containerRef.current,
      THREE: THREE,
      highlightColor: 0x6366f1,
      midtoneColor: 0x3b82f6,
      lowlightColor: 0x1e1b4b,
      baseColor: 0x0f172a,
      blurFactor: 0.5,
      speed: 1.5,
      zoom: 0.8,
      mouseControls: true,
      touchControls: true,
    });

    setEffect(vantaEffect);

    return () => {
      if (vantaEffect) vantaEffect.destroy();
    };
  }, []);

  return (
    <div
      ref={containerRef}
      style={{ width: "100%", height: "100vh", position: "relative" }}
    >
      <div style={{ position: "relative", zIndex: 1 }}>{children}</div>
    </div>
  );
}
```

### Switching Effects Dynamically

```js
let currentEffect = null;

function setBackground(effectModule, options) {
  if (currentEffect) currentEffect.destroy();

  currentEffect = effectModule({
    el: "#hero",
    THREE: THREE,
    ...options,
  });
}

// Usage
import WAVES from "vanta/dist/vanta.waves.min";
import BIRDS from "vanta/dist/vanta.birds.min";

setBackground(WAVES, { color: 0x0077be, waveHeight: 20 });
// Later...
setBackground(BIRDS, { color1: 0xff6600, backgroundColor: 0x111111 });
```

### Responsive Resize

Vanta.js handles window resizing automatically. If you dynamically change the container size (e.g., toggling a sidebar), call:

```js
effect.resize();
```

## Cleanup

Always destroy the effect when removing the element or navigating away:

```js
effect.destroy();
```

This removes the injected `<canvas>`, stops the animation loop, and releases the WebGL context.

In React, call `destroy()` in the `useEffect` cleanup function. In Vue, call it in `onBeforeUnmount`.

## Common Pitfalls

| Pitfall                             | Fix                                                                                                                                 |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| **Missing Three.js reference**      | Always pass `THREE: THREE` (or `p5` for cloud effects). Without it, the effect fails silently.                                      |
| **Effect not visible**              | Ensure the target element has a defined height (e.g., `height: 100vh`). A zero-height container produces an invisible canvas.       |
| **Content hidden behind canvas**    | Set `position: relative; z-index: 1` on content inside the container. The Vanta canvas is absolutely positioned inside the element. |
| **No cleanup on route change**      | Call `effect.destroy()` when the component unmounts. Leaked effects keep animating and consume GPU resources in the background.     |
| **Multiple effects on one element** | Destroy the existing effect before creating a new one. Layering effects doubles GPU load and causes visual glitches.                |
| **Poor mobile performance**         | Reduce `quantity`, `points`, or `speed`. Disable `gyroControls` if not needed. Some effects (HALO, GLOBE) are heavier than others.  |

## Best Practices

1. **Choose the right effect for the vibe.** `FOG` and `WAVES` are subtle and professional. `BIRDS` and `NET` are more dynamic. `GLOBE` works well for international / tech products.
2. **Keep colors consistent with your brand.** Pass your design system's hex values into `backgroundColor`, `color1`, and `color2`.
3. **Lazy-load the effect.** Import Vanta and Three.js dynamically so they don't block initial page load:
   ```js
   const [THREE, WAVES] = await Promise.all([
     import("three"),
     import("vanta/dist/vanta.waves.min"),
   ]);
   ```
4. **Reduce intensity on mobile.** Use `matchMedia` to detect small screens and pass lower values for `points`, `speed`, and `quantity`.
5. **Always destroy on unmount.** One leaked Vanta effect means a dangling WebGL context and continuous GPU usage.
6. **Test with DevTools Performance tab.** Animated backgrounds should stay well under 16 ms per frame. If they exceed this, simplify parameters.
7. **Respect reduced motion:**
   ```js
   const prefersReduced = matchMedia(
     "(prefers-reduced-motion: reduce)",
   ).matches;
   if (prefersReduced) {
     // Use a static gradient or skip the effect entirely
   }
   ```
8. **Use `minHeight` and `minWidth`** to prevent the canvas from collapsing on very small viewports.
