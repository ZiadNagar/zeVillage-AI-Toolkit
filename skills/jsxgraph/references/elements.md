# JSXGraph Elements — Deep Reference

All elements are created via `board.create(type, parents, attrs)`.
This file documents every significant element with full parent signatures.

---

## Geometry Primitives

### Point
```
board.create('point', [x, y], attrs)
board.create('point', [() => expr, () => expr], attrs)
```
**Key attrs:** `size`, `face` (`'o'`,`'x'`,`'+'`,`'<>'`,`'[]'`,`'^'`,`'v'`), `fixed`, `name`, `color`
**Methods:** `.X()`, `.Y()`, `.Dist(otherPoint)`, `.setPosition(JXG.COORDS_BY_USER,[x,y])`

### Glider
```
board.create('glider', [initX, initY, parentElement], attrs)
```
Constrained to move along `parentElement` (line, circle, curve).

### Line
```
board.create('line', [point1, point2], attrs)
board.create('line', [[x1,y1], [x2,y2]], attrs)
```
**Key attrs:** `straightFirst`, `straightLast` (bool — extend beyond points), `firstArrow`, `lastArrow`

### Segment
```
board.create('segment', [A, B], attrs)
board.create('segment', [[x1,y1],[x2,y2]], attrs)
```

### Circle
```
board.create('circle', [center, pointOnCircumference], attrs)
board.create('circle', [center, radiusNumber], attrs)
board.create('circle', [center, radiusFunction], attrs)
```
**Methods:** `.Radius()`, `.Area()`

### Arc
```
board.create('arc', [center, startPoint, endPoint], attrs)
board.create('arc', [center, startPoint, endPoint], { orthoType: 'square' })
```

### Sector
```
board.create('sector', [center, A, B], attrs)
```

### Polygon
```
board.create('polygon', [A, B, C, ...], attrs)
```
**attrs:** `fillColor`, `fillOpacity`, `borders` (for styling individual edges)

### RegularPolygon
```
board.create('regularpolygon', [A, B, n], attrs)  // n = number of vertices
```

### Ellipse
```
board.create('ellipse', [focus1, focus2, majorAxisPoint], attrs)
board.create('ellipse', [focus1, focus2, majorAxisLength], attrs)
```

### Hyperbola / Parabola
```
board.create('hyperbola', [focus1, focus2, vertex], attrs)
board.create('parabola', [focus, directrix], attrs)
```

### Conic
```
board.create('conic', [p1, p2, p3, p4, p5], attrs)  // 5-point conic
board.create('conic', [A, B, C, D, E, F], attrs)     // coefficients: Ax²+Bxy+Cy²+Dx+Ey+F=0
```

---

## Derived / Composition Elements

### Midpoint
```
board.create('midpoint', [A, B], attrs)
```

### Intersection
```
board.create('intersection', [el1, el2, n], attrs)  // n=0 first, n=1 second intersection
board.create('otherintersection', [el1, el2, knownIntersection], attrs)
```

### Perpendicular / Normal
```
board.create('perpendicular', [line, point], attrs)  // returns [perp_line, foot_point]
board.create('perpendicularpoint', [line, point], attrs)
board.create('normal', [curve, point], attrs)
```

### Parallel / Arrowparallel
```
board.create('parallel', [line, point], attrs)
board.create('arrowparallel', [line, point], attrs)
```

### Reflection / Mirror
```
board.create('reflection', [el, mirrorLine], attrs)
board.create('mirrorelement', [el, mirrorElement], attrs)
board.create('mirrorpoint', [point, mirrorPoint], attrs)
```

### Bisector
```
board.create('bisector', [A, B, C], attrs)   // bisects angle at B
board.create('bisectorlines', [line1, line2], attrs)  // returns [bis1, bis2]
```

### Circumcircle / Incircle
```
board.create('circumcircle', [A, B, C], attrs)  // returns [circumcenter, circle]
board.create('incircle',     [A, B, C], attrs)
board.create('circumcenter', [A, B, C], attrs)
board.create('incenter',     [A, B, C], attrs)
```

### Orthogonal Projection
```
board.create('orthogonalprojection', [point, line], attrs)
```

---

## Curves & Analysis

### Functiongraph
```
board.create('functiongraph', [f, xMin, xMax], attrs)
board.create('functiongraph', [f], attrs)  // auto x range from board
```
**Key attrs:** `doAdvancedPlot` (adaptive resolution, default true), `numberPointsLow`, `numberPointsHigh`

### Curve (general)
```
// Parametric: x(t), y(t)
board.create('curve', [xFn, yFn, tMin, tMax], attrs)
// Data: arrays
board.create('curve', [xArray, yArray], attrs)
```

### Implicit Curve
```
board.create('implicitcurve', [(x,y) => expr], attrs)
```

### Derivative
```
board.create('derivative', [functiongraphEl], attrs)
```

### Tangent / TangentTo
```
board.create('tangent',   [gliderOnCurve], attrs)
board.create('tangentto', [circle, externalPoint], attrs)
```

### Integral
```
board.create('integral', [[a, b], functiongraph], attrs)
// a, b can be numbers or functions
```

### Riemannsum
```
board.create('riemannsum', [f, n, type, a, b], attrs)
// type: 'left'|'right'|'middle'|'trapezoidal'|'simpson'|'upper'|'lower'|'random'|'infly'
```

### Slopefield
```
board.create('slopefield', [f, pointsArray], attrs)
board.create('slopefield', [(x,y) => expr], attrs)
```

### Vectorfield
```
board.create('vectorfield', [(x, y) => [vx, vy]], attrs)
```

### Spline / Cardinalspline / Metapostspline
```
board.create('spline', [pointsArray], attrs)
board.create('cardinalspline', [pointsArray, tau, type], attrs) // type: 'uniform'|'centripetal'
board.create('metapostspline', [pointsArray, tension], attrs)
```

### Stepfunction
```
board.create('stepfunction', [xArray, yArray], attrs)
```

---

## Interactive Controls

### Slider
```
board.create('slider',
  [[x1,y1], [x2,y2], [min, initial, max]],
  { name: 'k', snapWidth: 0, withTicks: true, suffixLabel: ' px', unitLabel: '' }
)
```
**Methods:** `.Value()`, `.setValue(v)`, `.setMin(v)`, `.setMax(v)`

### Button
```
board.create('button', [x, y, 'Label', callbackFn], attrs)
```

### Checkbox
```
board.create('checkbox', [x, y, 'Label'], attrs)
// checkbox.Value() returns bool
```

### Input
```
board.create('input', [x, y, 'prefix', 'initialValue'], attrs)
// input.Value() returns string
```

---

## Text & Annotation

### Text
```
board.create('text', [x, y, 'string'], attrs)
board.create('text', [x, y, () => dynamicString], attrs)
```
**Key attrs:** `fontSize`, `fontFamily`, `color`, `rotate`, `display` (`'html'`|`'internal'`),
`useMathjax`, `useKatex`, `parse`, `anchorX` (`'left'`|`'middle'`|`'right'`), `anchorY`

### Label
Elements auto-create labels. Access via `el.label`. Style via:
```js
board.create('point', [0, 0], {
  name: 'A',
  label: { fontSize: 16, color: 'red', offset: [10, -10] }
});
```

### Image
```
board.create('image', ['url/to/img.png', [x, y], [width, height]], attrs)
```

### Angle (Label)
```
board.create('angle', [A, B, C], {
  name: 'α',
  radius: 0.5,
  type: 'sector',    // 'sector'|'square'|'none'
  label: { fontSize: 14, offset: [5, 5] }
})
```

### Ticks
```
board.create('ticks', [line, majorHeight, minorHeight, labelFn], attrs)
// Or on a functiongraph
```

### Grid
```
board.create('grid', [], { gridX: 1, gridY: 1, strokeColor: '#ccc', strokeOpacity: 0.5 })
```

---

## Statistics / Data Viz

### Chart (Bar / Pie / Line / Spline)
```
board.create('chart', [dataArray | fnArray], {
  chartStyle: 'bar' | 'pie' | 'line' | 'spline' | 'point',
  width: 0.8,
  fillColor: colorOrArray,
  labels: labelArray,
  withLines: true  // for bar — connect bar tops with line
})
```

### Boxplot
```
board.create('boxplot',
  [q0, q1, q2, q3, q4],  // min, lower quartile, median, upper quartile, max
  { dir: 'vertical', width: 1, pos: 0 }
)
```

---

## Misc

### Group
```
const g = board.create('group', [A, B, C]);
// Moving one element in group moves all
```

### Tracecurve
```
board.create('tracecurve', [gliderPoint, tracePoint], attrs)
// Traces the path of tracePoint as glider moves
```

### Tapemeasure
```
board.create('tapemeasure', [[x1,y1],[x2,y2]], { name: 'd' })
// Draggable ruler showing distance
```

### Hatch
```
board.create('hatch', [segment, n], attrs)  // n ticks on segment
```

### Inequality
```
board.create('inequality', [line], { inverse: false })
// Shades the half-plane on one side of line
```

### ForeignObject
```
board.create('foreignobject', ['<p>HTML content</p>', [x,y], [width,height]], attrs)
```