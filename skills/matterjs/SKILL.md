---
name: matterjs
description: "Use this skill when building 2D physics simulations with Matter.js — rigid-body engines, collision detection, constraints, mouse interaction, and canvas rendering."
license: Complete terms in LICENSE.txt
---

# Matter.js

## When to Use

- Simulating 2D rigid-body physics (gravity, collisions, bouncing).
- Building interactive demos with draggable physics objects.
- Creating physics-based games or toys in the browser.
- Adding playful, physically-accurate motion to UI elements.
- Visualizing forces, constraints, or particle systems in 2D.

## Key Concepts

### Architecture

Matter.js is organized into modules:

| Module                      | Purpose                                                                 |
| --------------------------- | ----------------------------------------------------------------------- |
| `Engine`                    | Manages the physics simulation (world, timing, gravity).                |
| `World`                     | Deprecated alias — use `Composite` (the engine's root composite).       |
| `Render`                    | Built-in canvas renderer for quick prototyping.                         |
| `Runner`                    | Runs the engine at a fixed timestep using `requestAnimationFrame`.      |
| `Bodies`                    | Factory for creating rigid bodies (rectangle, circle, polygon, etc.).   |
| `Body`                      | Methods to manipulate a single body (apply force, set velocity).        |
| `Composite`                 | Container that holds bodies, constraints, and other composites.         |
| `Constraint`                | Connects two bodies (or a body to a point) with a spring or rigid link. |
| `Mouse` / `MouseConstraint` | Enables drag interaction.                                               |
| `Events`                    | Subscribe to collision, tick, and render events.                        |

### Installation

```bash
npm install matter-js
```

```js
import Matter from "matter-js";
```

Or use a CDN:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/matter-js/0.20.0/matter.min.js"></script>
```

### Core Setup

```js
const { Engine, Render, Runner, Bodies, Composite } = Matter;

// 1. Create engine
const engine = Engine.create();

// 2. Create renderer
const render = Render.create({
  element: document.getElementById("canvas-container"),
  engine: engine,
  options: {
    width: 800,
    height: 600,
    wireframes: false, // use solid fills
    background: "#1a1a2e",
  },
});

// 3. Run renderer and engine
Render.run(render);
const runner = Runner.create();
Runner.run(runner, engine);
```

### Creating Bodies

```js
// Static ground
const ground = Bodies.rectangle(400, 590, 800, 20, { isStatic: true });

// Dynamic circle
const ball = Bodies.circle(400, 100, 30, {
  restitution: 0.8, // bounciness (0–1)
  friction: 0.05,
  density: 0.002,
  render: { fillStyle: "#e94560" },
});

// Dynamic rectangle
const box = Bodies.rectangle(300, 50, 60, 60, {
  chamfer: { radius: 5 }, // rounded corners
  render: { fillStyle: "#0f3460" },
});

// Polygon
const pentagon = Bodies.polygon(500, 50, 5, 35, {
  render: { fillStyle: "#533483" },
});

// Add all to the world
Composite.add(engine.world, [ground, ball, box, pentagon]);
```

### Body Options

| Option        | Type         | Description                                       |
| ------------- | ------------ | ------------------------------------------------- |
| `isStatic`    | boolean      | If `true`, body is immovable (walls, floors).     |
| `restitution` | number (0–1) | Bounciness. 0 = no bounce, 1 = perfectly elastic. |
| `friction`    | number       | Surface friction. 0 = ice, 1 = rubber.            |
| `frictionAir` | number       | Air resistance. Default is `0.01`.                |
| `density`     | number       | Affects mass. Default `0.001`.                    |
| `angle`       | number       | Initial rotation in radians.                      |
| `chamfer`     | object       | `{ radius: n }` adds rounded corners.             |
| `render`      | object       | `{ fillStyle, strokeStyle, lineWidth, sprite }`.  |

### Mouse Interaction

```js
const { Mouse, MouseConstraint } = Matter;

const mouse = Mouse.create(render.canvas);
const mouseConstraint = MouseConstraint.create(engine, {
  mouse: mouse,
  constraint: {
    stiffness: 0.2,
    render: { visible: false },
  },
});

Composite.add(engine.world, mouseConstraint);
render.mouse = mouse; // keep render in sync
```

### Constraints

```js
const { Constraint } = Matter;

// Pin a body to a fixed point
const pin = Constraint.create({
  pointA: { x: 400, y: 50 },
  bodyB: ball,
  stiffness: 0.01,
  damping: 0.05,
  render: { strokeStyle: "#ffffff", lineWidth: 2 },
});

Composite.add(engine.world, pin);
```

### Collision Events

```js
const { Events } = Matter;

Events.on(engine, "collisionStart", (event) => {
  event.pairs.forEach(({ bodyA, bodyB }) => {
    console.log(`${bodyA.label} hit ${bodyB.label}`);
    // Flash color on collision
    bodyA.render.fillStyle = "#ffffff";
    setTimeout(() => {
      bodyA.render.fillStyle = "#e94560";
    }, 100);
  });
});
```

### Applying Forces

```js
const { Body } = Matter;

// One-time impulse (e.g., on click)
Body.applyForce(ball, ball.position, { x: 0.05, y: -0.05 });

// Set velocity directly
Body.setVelocity(ball, { x: 5, y: -8 });

// Set angular velocity (spin)
Body.setAngularVelocity(ball, 0.1);
```

### Gravity

```js
// Default gravity
engine.gravity.y = 1; // normal (downward)

// Zero gravity
engine.gravity.y = 0;

// Reverse gravity
engine.gravity.y = -1;

// Horizontal gravity
engine.gravity.x = 0.5;
engine.gravity.y = 0;
```

## Quick Recipes

### Bouncing Balls with Walls

```js
import Matter from "matter-js";

const { Engine, Render, Runner, Bodies, Composite, Mouse, MouseConstraint } =
  Matter;

const engine = Engine.create();
const render = Render.create({
  element: document.getElementById("app"),
  engine: engine,
  options: {
    width: 800,
    height: 600,
    wireframes: false,
    background: "#0d1117",
  },
});

// Walls
const walls = [
  Bodies.rectangle(400, 0, 800, 20, { isStatic: true }), // top
  Bodies.rectangle(400, 600, 800, 20, { isStatic: true }), // bottom
  Bodies.rectangle(0, 300, 20, 600, { isStatic: true }), // left
  Bodies.rectangle(800, 300, 20, 600, { isStatic: true }), // right
];

// Random bouncing balls
const colors = ["#e94560", "#0f3460", "#533483", "#16c79a", "#f5a623"];
const balls = Array.from({ length: 20 }, (_, i) =>
  Bodies.circle(
    100 + Math.random() * 600,
    50 + Math.random() * 200,
    10 + Math.random() * 20,
    {
      restitution: 0.9,
      friction: 0.01,
      render: { fillStyle: colors[i % colors.length] },
    },
  ),
);

Composite.add(engine.world, [...walls, ...balls]);

// Mouse drag
const mouse = Mouse.create(render.canvas);
const mc = MouseConstraint.create(engine, {
  mouse,
  constraint: { stiffness: 0.2, render: { visible: false } },
});
Composite.add(engine.world, mc);
render.mouse = mouse;

Render.run(render);
Runner.run(Runner.create(), engine);
```

### Newton's Cradle

```js
function createCradle(x, y, count, size, length) {
  const cradle = Composite.create({ label: "Cradle" });

  for (let i = 0; i < count; i++) {
    const px = x + i * (size * 2);
    const ball = Bodies.circle(px, y + length, size, {
      restitution: 1,
      friction: 0,
      frictionAir: 0,
      render: { fillStyle: "#e94560" },
    });
    const constraint = Constraint.create({
      pointA: { x: px, y },
      bodyB: ball,
      length,
      render: { strokeStyle: "#444" },
    });
    Composite.add(cradle, [ball, constraint]);
  }

  return cradle;
}

const cradle = createCradle(300, 50, 5, 20, 150);
Composite.add(engine.world, cradle);

// Pull first ball to start motion
const firstBall = cradle.bodies[0];
Body.setPosition(firstBall, {
  x: firstBall.position.x - 80,
  y: firstBall.position.y - 40,
});
```

## Cleanup Pattern

Always clean up when removing the simulation (e.g., in an SPA route change):

```js
function destroy() {
  Render.stop(render);
  Runner.stop(runner);
  Engine.clear(engine);

  // Remove the canvas element
  render.canvas.remove();
  render.textures = {};
}
```

React cleanup:

```jsx
useEffect(() => {
  // ... setup engine, render, runner ...

  return () => {
    Render.stop(render);
    Runner.stop(runner);
    Engine.clear(engine);
    render.canvas.remove();
    render.textures = {};
  };
}, []);
```

## Common Pitfalls

| Pitfall                                         | Fix                                                                                                                                            |
| ----------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bodies fall through thin walls**              | Make walls thicker (≥ 20 px) or reduce `engine.timing.timeScale`. Thin static bodies miss fast-moving objects.                                 |
| **Mouse constraint not working**                | Ensure `render.mouse = mouse` is set and the mouse is created from the correct canvas element.                                                 |
| **Bodies stick together**                       | Set `restitution` > 0 and `friction` low. Default friction can make objects cling on contact.                                                  |
| **No cleanup on unmount**                       | Call `Render.stop()`, `Runner.stop()`, `Engine.clear()`, and remove the canvas. Leaked engines keep ticking in the background.                 |
| **Performance with many bodies**                | Keep body count under ~200 for smooth 60 fps. Use `Body.setStatic(body, true)` for resting objects and `engine.enableSleeping = true`.         |
| **Tunnelling (fast bodies pass through walls)** | Increase `engine.positionIterations` and `engine.velocityIterations`, or use continuous collision detection by setting `body.isBullet = true`. |

## Best Practices

1. **Enable sleeping** for scenes with many bodies: `Engine.create({ enableSleeping: true })`. Sleeping bodies skip computation until disturbed.
2. **Use wireframes during development** (`wireframes: true`) to see collision boundaries, then switch to styled rendering.
3. **Label your bodies** with `label: "ground"`, `label: "player"` — it makes collision event handlers much more readable.
4. **Use `Composite.create()`** to group related bodies and constraints (e.g., a ragdoll, a vehicle) for easy addition / removal.
5. **Avoid `setInterval` for the physics loop.** Use `Runner` or call `Engine.update(engine, delta)` inside `requestAnimationFrame`.
6. **Tune `restitution`, `friction`, and `density`** in small increments. Physics parameters interact non-linearly — large jumps make debugging hard.
7. **Render with your own canvas** if you need custom visuals. Use `Events.on(engine, "afterUpdate", …)` and read body positions / angles to draw with Canvas 2D, PixiJS, or any other renderer.
8. **Respect reduced motion.** Disable or simplify physics animations when `prefers-reduced-motion: reduce` is active.
