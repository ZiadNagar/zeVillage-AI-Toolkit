---
name: jsxgraph
description: Expert-level JSXGraph (v1.12+) skill for creating interactive mathematical visualizations,
  geometric constructions, function graphs, charts, and 3D scenes in the browser.
  Use this skill whenever the user asks to build or improve anything with JSXGraph — including
  dynamic geometry, calculus visualizations (integrals, derivatives, Riemann sums), interactive
  sliders, parametric curves, polar plots, 3D views, charts (bar/pie/line), turtle graphics,
  or any embed of JSXGraph in HTML, React, or Webflow. Also trigger for questions about
  JSXGraph API methods, elements, attributes, or performance. Always use this skill before
  writing any JSXGraph code.
metadata:
  authors: "Ziad Elnagar"
---

# JSXGraph Skill (v1.12.2)

JSXGraph is an open-source, zero-dependency JavaScript library for interactive SVG/Canvas
math visualizations. Current stable: **1.12.2**.

## Table of Contents
1. [CDN & Setup](#1-cdn--setup)
2. [Board Initialization](#2-board-initialization)
3. [Core API — board.create()](#3-core-api--boardcreate)
4. [Element Reference](#4-element-reference)
5. [Styling & Attributes](#5-styling--attributes)
6. [Interactivity & Events](#6-interactivity--events)
7. [Calculus Features](#7-calculus-features)
8. [Charts](#8-charts)
9. [3D Views](#9-3d-views)
10. [Turtle Graphics](#10-turtle-graphics)
11. [Performance Best Practices](#11-performance-best-practices)
12. [Common Patterns](#12-common-patterns)

> **Deep references:** see `references/elements.md`, `references/attributes.md`, `references/3d.md`

---

## 1. CDN & Setup

```html
<!-- In <head> — always load BOTH CSS and JS -->
<link  rel="stylesheet" href="https://cdn.jsdelivr.net/npm/jsxgraph/distrib/jsxgraph.css"/>
<script src="https://cdn.jsdelivr.net/npm/jsxgraph/distrib/jsxgraphcore.js"></script>
```

**npm:**
```bash
npm install jsxgraph
```
```js
import JXG from 'jsxgraph';
import 'jsxgraph/distrib/jsxgraph.css';
```

**HTML container** — always give the div explicit dimensions:
```html
<div id="jxgbox" class="jxgbox" style="width:600px; height:400px;"></div>
```

---

## 2. Board Initialization

```js
const board = JXG.JSXGraph.initBoard('jxgbox', {
  boundingbox: [-5, 5, 5, -5],   // [xMin, yMax, xMax, yMin]
  keepaspectratio: true,          // 1:1 aspect — use for geometry
  axis: true,                     // show default X and Y axes
  showNavigation: true,           // zoom/pan controls bottom-right
  showCopyright: false,           // hide "JSXGraph vX.X.X" watermark
  pan: { enabled: true, needTwoFingers: false },
  zoom: { enabled: true, wheel: true, min: 0.1, max: 10 },
  grid: true,                     // show background grid
  defaultAxes: {                  // customize axis labels
    x: { name: 'x', withLabel: true },
    y: { name: 'y', withLabel: true }
  }
});
```

### Key Board Attributes

| Attribute | Type | Default | Description |
|---|---|---|---|
| `boundingbox` | `[l,t,r,b]` | `[-5,5,5,-5]` | Viewport in user coordinates |
| `keepaspectratio` | bool | `false` | Lock to 1:1 pixel ratio |
| `axis` | bool | `false` | Auto-create X/Y axes |
| `grid` | bool | `false` | Background grid |
| `pan.enabled` | bool | `true` | Allow mouse pan |
| `zoom.wheel` | bool | `false` | Mouse-wheel zoom |
| `showNavigation` | bool | `true` | Nav buttons |
| `showCopyright` | bool | `true` | Version watermark |
| `renderer` | string | `'auto'` | `'svg'`\|`'canvas'`\|`'no'` |

### Destroy / Replace a Board
```js
JXG.JSXGraph.freeBoard(board);  // clean up before re-initializing
const board2 = JXG.JSXGraph.initBoard('jxgbox', { ... });
```

---

## 3. Core API — board.create()

**The one method to rule them all:**
```js
const el = board.create(elementType, parents, attributes);
```

- `elementType` — lowercase string: `'point'`, `'line'`, `'circle'`, etc.
- `parents` — array of coordinates, existing elements, or functions
- `attributes` — optional object with styling and behavioral options

**Dynamic values via functions** — any attribute value or parent coordinate can be a function:
```js
const p = board.create('point', [() => slider.Value(), 0], { name: 'P' });
```

**Updating elements:**
```js
el.setAttribute({ strokeColor: 'red', strokeWidth: 3 });
el.setPosition(JXG.COORDS_BY_USER, [2, 3]);  // move a point
board.update();  // force redraw
```

**Removing elements:**
```js
board.removeObject(el);
```

**Selecting elements:**
```js
board.select({ strokeColor: 'red' }).setAttribute({ visible: false });
board.select(el => el.elementType === 'point');
```

---

## 4. Element Reference

See `references/elements.md` for full signature list. Quick reference:

### Points
```js
// Fixed point
const A = board.create('point', [1, 2], { name: 'A', fixed: false, size: 4 });

// Point defined by function
const B = board.create('point', [() => A.X() + 1, () => A.Y()], { name: 'B' });

// Glider (constrained to a curve/line/circle)
const G = board.create('glider', [0, 0, line], { name: 'G' });

// Midpoint
const M = board.create('midpoint', [A, B], { name: 'M' });

// Intersection of two curves/lines
const I = board.create('intersection', [line1, circle1, 0]);  // 0 = first intersection
```

Point `face` values: `'o'` (circle), `'x'`, `'+'`, `'<>'`, `'[]'`, `'^'`, `'v'`

### Lines, Segments, Rays
```js
// Infinite line through two points
const line = board.create('line', [A, B], { strokeColor: '#333' });

// Segment (finite line)
const seg = board.create('segment', [A, B]);

// Arrow
const arr = board.create('arrow', [A, B]);

// Perpendicular / Parallel
const perp = board.create('perpendicular', [line, A]);
const par  = board.create('parallel',      [line, A]);
```

### Circles & Arcs
```js
// Circle by center + point on circumference
const c1 = board.create('circle', [center, pointOnCirc]);

// Circle by center + radius (number)
const c2 = board.create('circle', [center, 3]);

// Arc: center, start-point, end-point
const arc = board.create('arc', [center, A, B]);

// Circumcircle of a triangle
const [circumCenter, circumCirc] = board.create('circumcircle', [A, B, C]);
```

### Polygons
```js
const poly  = board.create('polygon', [A, B, C], { fillColor: '#eef', fillOpacity: 0.5 });
const rect  = board.create('polygon', [A, B, C, D]);
const rPoly = board.create('regularpolygon', [A, B, 5]);  // pentagon
```

### Curves & Function Graphs
```js
// y = f(x)
const fg = board.create('functiongraph', [x => Math.sin(x), -Math.PI, Math.PI], {
  strokeColor: '#c00', strokeWidth: 2
});

// Parametric curve [x(t), y(t)]
const param = board.create('curve',
  [t => Math.cos(t), t => Math.sin(t), 0, 2 * Math.PI]
);

// Polar curve r(φ)
const polar = board.create('curve',
  [t => Math.cos(2 * t) * Math.cos(t),  // x = r·cos(φ)
   t => Math.cos(2 * t) * Math.sin(t),  // y = r·sin(φ)
   0, 2 * Math.PI]
);

// Implicit curve f(x,y) = 0
const impl = board.create('implicitcurve', [(x, y) => x*x + y*y - 4], {
  strokeColor: 'blue'
});

// Data-driven curve
const dataCurve = board.create('curve', [[0,1,2,3], [0,1,4,9]]);
```

### Sliders
```js
// [startPos, endPos, [min, initial, max]]
const s = board.create('slider',
  [[1, 3], [5, 3], [0, 1, 10]],
  { name: 'n', snapWidth: 1, label: { fontSize: 14 } }
);
// Read value: s.Value()
```

### Text & Labels
```js
// Static text
board.create('text', [1, 2, 'Hello JSXGraph']);

// Dynamic text with function
board.create('text', [1, 2, () => `x = ${A.X().toFixed(2)}`]);

// MathJax / KaTeX
board.create('text', [0, 0, '\\(\\int_0^1 x^2\\,dx\\)'], {
  useMathjax: true, parse: false, fontSize: 18
});
```

### Angles
```js
// Angle at vertex B, from A to C
const angle = board.create('angle', [A, B, C], {
  name: 'α', radius: 0.5,
  label: { fontSize: 14, color: 'purple' }
});
// angle.Value() returns angle in radians
```

### Transformations
```js
const t = board.create('transform', [2, 3], { type: 'translate' });
const r = board.create('transform', [Math.PI / 4], { type: 'rotate' });
const s = board.create('transform', [2, 2], { type: 'scale' });

// Apply to element
const B = board.create('point', [A, [t]]);
```

---

## 5. Styling & Attributes

### Universal Attributes (all elements)
```js
{
  visible: true,
  fixed: false,            // prevent dragging
  name: 'P',
  id: 'myPoint',
  color: '#c00',           // shorthand — sets stroke & fill
  strokeColor: '#333',
  strokeWidth: 2,
  strokeOpacity: 1,
  fillColor: 'yellow',
  fillOpacity: 0.3,
  highlightStrokeColor: 'blue',
  highlightFillColor: 'lightblue',
  dash: 0,                 // 0=solid, 1–7 dash patterns; 7=dotted (needs linecap:'round')
  shadow: false,
  layer: 2,                // z-order (0–9)
  label: {
    fontSize: 14,
    color: '#333',
    position: 'rt',        // relative to parent: 'rt','lft','top','bot','urt','ulft'…
    offset: [5, 5]         // [x,y] pixel offset
  },
  trace: false,            // leave a trail as the element moves
  withLabel: true,
  cssClass: '',            // apply CSS class (SVG renderer only)
}
```

### Global Defaults
```js
// Change defaults BEFORE initBoard
JXG.Options.point.size = 4;
JXG.Options.point.color = '#2255aa';
JXG.Options.line.strokeWidth = 2;
JXG.Options.text.fontSize = 14;
```

---

## 6. Interactivity & Events

### Board Events
```js
board.on('update', () => console.log('board updated'));
board.on('mousedown', (e) => {
  const coords = board.getUsrCoordsOfMouse(e);
  console.log(coords);
});
```

### Element Events
```js
A.on('drag', () => {
  textEl.setText(`A = (${A.X().toFixed(2)}, ${A.Y().toFixed(2)})`);
});

A.on('mousedown', () => A.setAttribute({ size: 8 }));
A.on('mouseup',   () => A.setAttribute({ size: 4 }));
```

### HTML Inputs (Button / Checkbox / Input)
```js
const btn = board.create('button', [1, 2, 'Click me', () => {
  A.moveTo([0, 0], 500);  // animate to position in 500ms
}]);

const cb = board.create('checkbox', [1, 1, 'Show circle'], {
  onChange: () => circle.setAttribute({ visible: cb.Value() })
});

const inp = board.create('input', [-3, 3, 'n=', '5'], {
  onChange: () => { /* react to user input */ }
});
// inp.Value() returns the current string
```

### Animation
```js
point.moveTo([3, 4], 800);  // move to [3,4] in 800ms
point.moveAlong(curve, 2000, { callback: () => console.log('done') });

// Manual animation loop
let t = 0;
const interval = setInterval(() => {
  t += 0.05;
  A.setPosition(JXG.COORDS_BY_USER, [Math.cos(t), Math.sin(t)]);
  board.update();
}, 30);
```

---

## 7. Calculus Features

### Derivative
```js
const f  = board.create('functiongraph', [x => Math.sin(x)], { strokeColor: 'green' });
const df = board.create('functiongraph', [board.D(x => Math.sin(x))], { strokeColor: 'orange' });
// Or use Derivative element:
const deriv = board.create('derivative', [f], { strokeColor: 'orange', dash: 2 });
```

### Tangent
```js
const glider  = board.create('glider', [1, 0, f]);
const tangent = board.create('tangent', [glider], { strokeColor: '#900' });
```

### Integral
```js
const integral = board.create('integral', [[-Math.PI, Math.PI], f], {
  fillColor: '#80a0ff', fillOpacity: 0.4,
  label: { fontSize: 16 }
});
// integral.Value() returns the numerical area
```

### Riemann Sum
```js
const n     = board.create('slider', [[1,4],[5,4],[1,20,50]], { name: 'n', snapWidth: 1 });
const riemann = board.create('riemannsum',
  [x => Math.sin(x), () => n.Value(), 'left', -Math.PI, Math.PI],
  { fillColor: '#ff8800', fillOpacity: 0.3 }
);
// Method options: 'left', 'right', 'middle', 'trapezoidal', 'simpson', 'upper', 'lower'
```

### ODE / Slope Field
```js
// Slope field for dy/dx = f(x,y)
const field = board.create('slopefield',
  [(x, y) => -x / y],
  { strokeColor: '#aaa', strokeWidth: 1 }
);

// Numerical solution via RK4 (built-in)
const ode = JXG.Math.Numerics.rungeKutta('rk4',
  [1],                           // initial y value
  [0, 5],                        // t range
  100,                           // steps
  (t, y) => [y[0] * 0.5]        // dy/dt
);
```

### Spline Interpolation
```js
const pts = [A, B, C, D];
const spline = board.create('spline', pts, { strokeColor: 'blue', strokeWidth: 2 });
// Cardinal spline:
const cs = board.create('cardinalspline', [pts, 0.5, 'centripetal']);
```

---

## 8. Charts

```js
// Bar chart
const chart = board.create('chart',
  [[2, 4, 1, 3, 5]],
  {
    chartStyle: 'bar',
    width: 0.8,
    fillColor: ['#c00','#0c0','#00c','#c90','#90c'],
    labels: ['A','B','C','D','E'],
    label: { fontSize: 12 }
  }
);

// Pie chart
board.create('chart',
  [[20, 35, 15, 30]],
  {
    chartStyle: 'pie',
    labels: ['Q1','Q2','Q3','Q4'],
    fillColor: ['#e55','#5e5','#55e','#ee5']
  }
);

// Line chart
board.create('chart', [[1,3,2,5,4]], { chartStyle: 'line', strokeColor: 'blue' });
```

---

## 9. 3D Views

See `references/3d.md` for full detail.

```js
// Container: bounding box is the 2D board
const board = JXG.JSXGraph.initBoard('jxgbox', {
  boundingbox: [-5, 5, 5, -5],
  keepaspectratio: true
});

// Create a 3D view inside the 2D board
const view = board.create('view3d',
  [[-4, -3],       // 2D position of view origin (pixels or board units)
   [8, 8],         // 2D size of the view
   [[-5,5],[-5,5],[-5,5]]  // 3D bounding box [xRange, yRange, zRange]
  ],
  {
    xPlaneRear: { visible: true },
    yPlaneRear: { visible: true },
    zPlaneRear: { visible: true }
  }
);

// 3D axes
view.create('axis3d', [[0,0,0],[1,0,0]], { name: 'x', strokeColor: 'red' });
view.create('axis3d', [[0,0,0],[0,1,0]], { name: 'y', strokeColor: 'green' });
view.create('axis3d', [[0,0,0],[0,0,1]], { name: 'z', strokeColor: 'blue' });

// 3D point
const p3 = view.create('point3d', [1, 2, 3], { name: 'P', size: 4 });

// 3D curve (parametric)
view.create('curve3d',
  [t => Math.cos(t), t => Math.sin(t), t => t * 0.3, 0, 6 * Math.PI]
);

// 3D surface z = f(x,y)
view.create('functiongraph3d',
  [(x, y) => Math.sin(Math.sqrt(x*x + y*y))],
  { xRange: [-5, 5], yRange: [-5, 5], stepsX: 40, stepsY: 40 }
);

// Parametric 3D surface [x(u,v), y(u,v), z(u,v)]
view.create('parametricsurface3d',
  [(u, v) => Math.cos(u) * (3 + Math.cos(v)),
   (u, v) => Math.sin(u) * (3 + Math.cos(v)),
   (u, v) => Math.sin(v)],
  { uRange: [0, 2*Math.PI], vRange: [0, 2*Math.PI], stepsU: 40, stepsV: 20 }
);
```

---

## 10. Turtle Graphics

```js
const board = JXG.JSXGraph.initBoard('jxgbox', {
  boundingbox: [-200, 200, 200, -200], axis: false
});
const t = board.create('turtle', [0, 0, 90]); // [x, y, heading°]

// Movement commands
t.forward(100);  // or t.fd(100)
t.back(50);      // or t.bk(50)
t.right(90);     // or t.rt(90)
t.left(45);      // or t.lt(45)
t.penUp();       // stop drawing
t.penDown();     // resume drawing
t.setPos(0, 0);
t.home();
t.clean();       // clear trace, keep turtle
t.clearScreen(); // clear everything

// Style
t.setPenColor('blue');
t.setPenSize(2);
t.setStrokeColor('#900');

// Koch snowflake example
function koch(t, n, len) {
  if (n === 0) { t.fd(len); return; }
  koch(t, n-1, len/3);
  t.lt(60);
  koch(t, n-1, len/3);
  t.rt(120);
  koch(t, n-1, len/3);
  t.lt(60);
  koch(t, n-1, len/3);
}
```

---

## 11. Performance Best Practices

```js
// ✅ ALWAYS suspend/unsuspend around bulk creation
board.suspendUpdate();
for (let i = 0; i < 1000; i++) {
  board.create('point', [i * 0.1, Math.sin(i * 0.1)], { withLabel: false });
}
board.unsuspendUpdate();

// ✅ Cache element references — never use getElementById in JSXGraph context
const myPoint = board.create('point', [0, 0]);
// Use myPoint directly, not board.select('#id') in hot paths

// ✅ Use snapWidth on sliders to reduce update frequency
slider = board.create('slider', [[0,0],[5,0],[0,1,100]], { snapWidth: 0.1 });

// ✅ Use board.D() for numerical differentiation instead of manual finite differences
const df = board.D(f);  // returns a function

// ✅ For function graphs, control resolution explicitly
board.create('functiongraph', [f], { numberPointsLow: 200, numberPointsHigh: 1000 });

// ✅ Hide elements instead of destroying/recreating when toggling
el.setAttribute({ visible: false });   // hide
el.setAttribute({ visible: true });    // show

// ✅ Set doAdvancedPlot false for simple, smooth functions (faster)
board.create('functiongraph', [f], { doAdvancedPlot: false });

// ✅ Avoid closures in tight loops — extract loop variable
for (let i = 0; i < n; i++) {
  const xi = i;  // capture correctly
  board.create('point', [xi, Math.sin(xi)]);
}
```

---

## 12. Common Patterns

### Responsive Board
```html
<div id="jxgbox" class="jxgbox" style="width:100%; aspect-ratio: 1;"></div>
```
```js
window.addEventListener('resize', () => board.resizeContainer(
  document.getElementById('jxgbox').offsetWidth,
  document.getElementById('jxgbox').offsetHeight,
  true
));
```

### React Integration
```jsx
import { useEffect, useRef } from 'react';
import JXG from 'jsxgraph';
import 'jsxgraph/distrib/jsxgraph.css';

function JSXBoard({ id }) {
  const boardRef = useRef(null);
  useEffect(() => {
    const board = JXG.JSXGraph.initBoard(id, {
      boundingbox: [-5, 5, 5, -5],
      axis: true
    });
    boardRef.current = board;
    board.create('functiongraph', [x => Math.sin(x)]);
    return () => JXG.JSXGraph.freeBoard(board);
  }, [id]);

  return <div id={id} className="jxgbox" style={{ width: '100%', height: '400px' }} />;
}
```

### Dynamic Construction from Data
```js
function plotData(xData, yData) {
  board.suspendUpdate();
  board.removeObject(board.select(el => el.elementType === 'curve'));
  board.create('curve', [xData, yData], { strokeColor: 'blue', strokeWidth: 2 });
  board.unsuspendUpdate();
}
```

### Coordinate Display on Hover
```js
board.create('text', [
  () => board.getBoundingBox()[0] + 0.2,
  () => board.getBoundingBox()[1] - 0.3,
  () => {
    const p = board.mouseCoords;
    return p ? `(${p.usrCoords[1].toFixed(2)}, ${p.usrCoords[2].toFixed(2)})` : '';
  }
], { fontSize: 12, fixed: true });
```

### Euler Line Construction (Full Example)
```js
const board = JXG.JSXGraph.initBoard('jxgbox', {
  boundingbox: [-6, 6, 6, -6], keepaspectratio: true
});

const A = board.create('point', [1, 0],   { name: 'A', color: 'red' });
const B = board.create('point', [-1, 0],  { name: 'B', color: 'red' });
const C = board.create('point', [0.2, 2], { name: 'C', color: 'red' });

board.create('polygon', [A, B, C], { fillColor: '#ffd', fillOpacity: 0.5 });

// Altitudes → orthocenter H
const ha = board.create('perpendicular', [board.create('line',[B,C]), A]);
const hb = board.create('perpendicular', [board.create('line',[A,C]), B]);
const H  = board.create('intersection', [ha[0], hb[0], 0], { name: 'H', color: 'blue' });

// Medians → centroid G
const Mc = board.create('midpoint', [A, B], { name: 'Mc', size: 2 });
const Ma = board.create('midpoint', [B, C], { name: 'Ma', size: 2 });
const G  = board.create('intersection', [
  board.create('line', [Ma, A]),
  board.create('line', [Mc, C]),
  0
], { name: 'G', color: 'green' });

// Circumcircle → circumcenter U
const [U] = board.create('circumcircle', [A, B, C], { name: ['U',''] });

// Euler line through H, G, U
board.create('line', [H, U], {
  strokeColor: '#901B77', strokeWidth: 2, name: 'Euler Line', withLabel: true
});
```

---

## Quick Cheat-Sheet

| Need | Code |
|---|---|
| Init board | `JXG.JSXGraph.initBoard('id', { boundingbox: [...] })` |
| Create element | `board.create('elementType', parents, attrs)` |
| Read slider | `slider.Value()` |
| Read point coords | `p.X()`, `p.Y()` |
| Dynamic text | `() => \`value: ${x.toFixed(2)}\`` |
| Numerical derivative | `board.D(f)` |
| Suspend redraw | `board.suspendUpdate()` / `board.unsuspendUpdate()` |
| Destroy board | `JXG.JSXGraph.freeBoard(board)` |
| Move point animated | `p.moveTo([x,y], durationMs)` |
| Force redraw | `board.update()` |