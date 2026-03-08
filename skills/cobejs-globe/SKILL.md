---
name: cobejs-globe
description: "Use this skill when building lightweight interactive WebGL globe visualizations with cobe.js — marker placement, auto-rotation, responsive sizing, and framework integration."
license: Complete terms in LICENSE.txt
---

# cobe.js Globe

## When to Use

- Displaying an interactive rotating globe on a landing page or dashboard.
- Showing geographic data points (offices, users, servers) as markers on a 3D globe.
- Needing a lightweight (~5 KB) alternative to full Three.js globe solutions.
- Building "where we operate" or "global presence" sections.

## Key Concepts

### What Is cobe.js

cobe is a tiny WebGL globe renderer. It draws a dotted globe on a `<canvas>` element with smooth auto-rotation, configurable markers, and mouse/touch drag interaction — all in roughly 5 KB gzipped.

### Installation

```bash
npm install cobe
```

Or via CDN:

```html
<script src="https://unpkg.com/cobe"></script>
```

### Core API

```js
import createGlobe from "cobe";

const globe = createGlobe(canvasElement, {
  devicePixelRatio: 2,
  width: 600, // logical width (canvas is sized to width × devicePixelRatio)
  height: 600,
  phi: 0, // initial horizontal rotation (radians)
  theta: 0.3, // initial vertical tilt (radians)
  dark: 1, // 0 = light mode, 1 = dark mode
  diffuse: 1.2, // surface shading intensity
  mapSamples: 16000, // number of dots on the globe surface
  mapBrightness: 6, // brightness of land dots
  baseColor: [0.3, 0.3, 0.3], // RGB (0–1) for ocean/base
  markerColor: [0.1, 0.8, 1], // RGB (0–1) for markers
  glowColor: [0.05, 0.05, 0.2], // RGB (0–1) for atmospheric glow
  markers: [
    { location: [37.7749, -122.4194], size: 0.06 }, // San Francisco
    { location: [51.5074, -0.1278], size: 0.06 }, // London
    { location: [35.6762, 139.6503], size: 0.04 }, // Tokyo
  ],
  onRender: (state) => {
    // Called every frame — use to animate rotation
    state.phi += 0.005;
  },
});
```

### Configuration Options

| Option             | Type         | Default         | Description                                                                      |
| ------------------ | ------------ | --------------- | -------------------------------------------------------------------------------- |
| `devicePixelRatio` | number       | 1               | Canvas resolution multiplier. Use `window.devicePixelRatio` for crisp rendering. |
| `width`            | number       | 600             | Logical width of the canvas.                                                     |
| `height`           | number       | 600             | Logical height of the canvas.                                                    |
| `phi`              | number       | 0               | Initial horizontal rotation in radians.                                          |
| `theta`            | number       | 0               | Initial vertical tilt in radians.                                                |
| `dark`             | number (0–1) | 1               | Dark mode intensity (0 = light, 1 = dark).                                       |
| `diffuse`          | number       | 1.2             | Controls the diffuse lighting on the globe surface.                              |
| `mapSamples`       | number       | 16000           | Number of dots drawn on land masses. Higher = more detailed but slower.          |
| `mapBrightness`    | number       | 6               | Brightness of country/land dots.                                                 |
| `baseColor`        | [r, g, b]    | [0.3, 0.3, 0.3] | Ocean / base color (values 0–1).                                                 |
| `markerColor`      | [r, g, b]    | [1, 0.5, 1]     | Marker dot color (values 0–1).                                                   |
| `glowColor`        | [r, g, b]    | [1, 1, 1]       | Atmospheric glow around the globe edge.                                          |
| `markers`          | array        | []              | Array of `{ location: [lat, lng], size: number }` objects.                       |
| `onRender`         | function     | —               | Callback invoked every frame with a mutable `state` object.                      |
| `offset`           | [x, y]       | [0, 0]          | Pixel offset for positioning the globe within the canvas.                        |
| `scale`            | number       | 1               | Scale factor of the globe.                                                       |

### Markers

Each marker is defined by a geographic coordinate and a size:

```js
markers: [
  { location: [40.7128, -74.006], size: 0.08 }, // New York
  { location: [48.8566, 2.3522], size: 0.06 }, // Paris
  { location: [-33.8688, 151.2093], size: 0.05 }, // Sydney
];
```

- `location` is `[latitude, longitude]` in decimal degrees.
- `size` controls the marker radius relative to the globe (0.03–0.1 is typical).

### Auto-Rotation

Animate rotation inside the `onRender` callback:

```js
onRender: (state) => {
  state.phi += 0.003; // horizontal rotation speed
};
```

To pause rotation on hover or drag, track a flag:

```js
let pointerDown = false;
canvas.addEventListener("pointerdown", () => (pointerDown = true));
canvas.addEventListener("pointerup", () => (pointerDown = false));

onRender: (state) => {
  if (!pointerDown) {
    state.phi += 0.003;
  }
};
```

### Cleanup

`createGlobe()` returns a destroy function:

```js
const globe = createGlobe(canvas, {
  /* options */
});

// When done:
globe.destroy();
```

Always call `destroy()` when removing the globe (SPA route change, component unmount).

## Quick Recipes

### Interactive Globe with Markers

```html
<canvas
  id="globe-canvas"
  style="width: 600px; height: 600px; max-width: 100%; aspect-ratio: 1;"
></canvas>

<script type="module">
  import createGlobe from "cobe";

  const canvas = document.getElementById("globe-canvas");

  let phi = 0;

  const globe = createGlobe(canvas, {
    devicePixelRatio: Math.min(window.devicePixelRatio, 2),
    width: canvas.offsetWidth * 2,
    height: canvas.offsetWidth * 2,
    phi: 0,
    theta: 0.25,
    dark: 1,
    diffuse: 1.2,
    mapSamples: 16000,
    mapBrightness: 6,
    baseColor: [0.15, 0.15, 0.2],
    markerColor: [0.39, 0.4, 0.95], // indigo-ish
    glowColor: [0.08, 0.08, 0.15],
    markers: [
      { location: [37.7749, -122.4194], size: 0.06 }, // San Francisco
      { location: [51.5074, -0.1278], size: 0.06 }, // London
      { location: [35.6762, 139.6503], size: 0.05 }, // Tokyo
      { location: [1.3521, 103.8198], size: 0.04 }, // Singapore
      { location: [-23.5505, -46.6333], size: 0.05 }, // Sao Paulo
      { location: [30.0444, 31.2357], size: 0.05 }, // Cairo
    ],
    onRender: (state) => {
      state.phi = phi;
      phi += 0.003;
    },
  });

  // Cleanup on page unload
  window.addEventListener("beforeunload", () => globe.destroy());
</script>
```

### Responsive Globe

```js
function createResponsiveGlobe(canvas, markers) {
  let globe;
  let phi = 0;

  function init() {
    const width = canvas.offsetWidth;
    const dpr = Math.min(window.devicePixelRatio, 2);

    if (globe) globe.destroy();

    globe = createGlobe(canvas, {
      devicePixelRatio: dpr,
      width: width * dpr,
      height: width * dpr,
      phi: phi,
      theta: 0.2,
      dark: 1,
      diffuse: 1.2,
      mapSamples: width < 400 ? 8000 : 16000, // fewer dots on small screens
      mapBrightness: 6,
      baseColor: [0.2, 0.2, 0.25],
      markerColor: [0.4, 0.8, 1],
      glowColor: [0.05, 0.05, 0.15],
      markers: markers,
      onRender: (state) => {
        state.phi = phi;
        phi += 0.003;
      },
    });
  }

  init();

  const resizeObserver = new ResizeObserver(() => init());
  resizeObserver.observe(canvas);

  return {
    destroy() {
      resizeObserver.disconnect();
      if (globe) globe.destroy();
    },
  };
}
```

### React Component

```jsx
import { useEffect, useRef } from "react";
import createGlobe from "cobe";

export default function Globe({ markers = [], className }) {
  const canvasRef = useRef(null);

  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;

    let phi = 0;
    const width = canvas.offsetWidth;
    const dpr = Math.min(window.devicePixelRatio, 2);

    canvas.width = width * dpr;
    canvas.height = width * dpr;

    const globe = createGlobe(canvas, {
      devicePixelRatio: dpr,
      width: width * dpr,
      height: width * dpr,
      phi: 0,
      theta: 0.25,
      dark: 1,
      diffuse: 1.2,
      mapSamples: 16000,
      mapBrightness: 6,
      baseColor: [0.15, 0.15, 0.2],
      markerColor: [0.39, 0.4, 0.95],
      glowColor: [0.08, 0.08, 0.15],
      markers,
      onRender: (state) => {
        state.phi = phi;
        phi += 0.003;
      },
    });

    return () => globe.destroy();
  }, [markers]);

  return (
    <canvas
      ref={canvasRef}
      className={className}
      style={{ width: "100%", maxWidth: 600, aspectRatio: "1" }}
    />
  );
}
```

Usage:

```jsx
<Globe
  markers={[
    { location: [37.77, -122.42], size: 0.06 },
    { location: [51.51, -0.13], size: 0.06 },
  ]}
  className="mx-auto"
/>
```

## Common Pitfalls

| Pitfall                              | Fix                                                                                                                 |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------- |
| **Blurry globe on high-DPI screens** | Set `devicePixelRatio: Math.min(window.devicePixelRatio, 2)` and size the canvas accordingly (`width * dpr`).       |
| **Globe does not appear**            | Ensure the canvas has a non-zero width and height. Inline `style="width: 600px; height: 600px"` or use CSS.         |
| **No cleanup on unmount**            | Always call `globe.destroy()` in `useEffect` cleanup or equivalent. Leaked globes keep rendering in the background. |
| **Markers at wrong positions**       | Coordinates are `[latitude, longitude]` — not `[lng, lat]`. Latitude ranges ±90, longitude ±180.                    |
| **Performance on mobile**            | Lower `mapSamples` (e.g., 8000) and cap `devicePixelRatio` at 2.                                                    |
| **Globe resets rotation on resize**  | Track `phi` in an outer variable and pass it into the re-created globe so rotation continues smoothly.              |

## Best Practices

1. **Cap `devicePixelRatio` at 2.** Values above 2 quadruple pixel count with negligible visual benefit.
2. **Use `mapSamples` wisely.** 16000 looks good on desktop; reduce to 8000–10000 on mobile for smoother frame rates.
3. **Track `phi` outside the globe instance.** This allows seamless rotation continuity across resizes or re-renders.
4. **Match colors to your design system.** Convert hex colors to normalized RGB (`[r/255, g/255, b/255]`).
5. **Lazy-load cobe.** The globe is rarely above the fold — dynamically import it when the section scrolls into view:
   ```js
   const { default: createGlobe } = await import("cobe");
   ```
6. **Respect reduced motion:**
   ```js
   const reduced = matchMedia("(prefers-reduced-motion: reduce)").matches;
   onRender: (state) => {
     if (!reduced) state.phi += 0.003;
   };
   ```
7. **Use `ResizeObserver`** instead of `window.resize` for responsive re-initialization — it fires only when the actual container changes size.
8. **Keep marker sizes proportional.** Sizes between 0.03 and 0.1 work best. Larger markers overlap and become unreadable.
