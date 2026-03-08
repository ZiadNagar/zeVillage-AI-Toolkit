---
name: gsap-animation
description: "Use this skill when building web animations with GSAP — tweens, timelines, ScrollTrigger scroll-driven effects, stagger sequences, and easing curves."
license: Complete terms in LICENSE.txt
---

# GSAP Animation

## When to Use

- Animating DOM elements with precise timing (entrance, exit, state change).
- Building multi-step animation sequences with timelines.
- Creating scroll-driven animations (parallax, pin, scrub).
- Staggering a list of child elements for choreographed reveals.
- Replacing CSS transitions when you need fine-grained control, callbacks, or sequencing.

## Key Concepts

### Tweens

A tween is a single animation applied to one or more targets.

| Method | Behaviour |
|---|---|
| `gsap.to(target, vars)` | Animate **from** current values **to** the specified values. |
| `gsap.from(target, vars)` | Animate **from** the specified values **to** current values. |
| `gsap.fromTo(target, fromVars, toVars)` | Explicitly define both start and end values. |
| `gsap.set(target, vars)` | Immediately set properties (zero-duration tween). |

### Timelines

A timeline is a sequencing container for tweens.

```js
const tl = gsap.timeline({ defaults: { duration: 0.6, ease: "power2.out" } });

tl.from(".hero-title", { y: 40, opacity: 0 })
  .from(".hero-subtitle", { y: 30, opacity: 0 }, "-=0.3")   // overlap by 0.3s
  .from(".hero-cta", { scale: 0.8, opacity: 0 }, "-=0.2");
```

Position parameters control overlap:

| Parameter | Meaning |
|---|---|
| `"-=0.3"` | Start 0.3 s before previous tween ends (overlap). |
| `"+=0.5"` | Start 0.5 s after previous tween ends (gap). |
| `"<"` | Start at same time as previous tween. |
| `2` | Absolute time of 2 s on the timeline. |

### Easing

GSAP ships a rich library of eases.

| Ease | Use Case |
|---|---|
| `"power2.out"` | Natural deceleration — good default for entrances. |
| `"power2.in"` | Acceleration — exits flying off-screen. |
| `"power2.inOut"` | Symmetric — state-change morphs. |
| `"back.out(1.7)"` | Slight overshoot — playful UI elements. |
| `"elastic.out(1, 0.3)"` | Spring-like bounce — attention-grabbers. |
| `"none"` / `"linear"` | Constant speed — progress bars, looping rotations. |

### ScrollTrigger

Register the plugin before use:

```js
import { ScrollTrigger } from "gsap/ScrollTrigger";
gsap.registerPlugin(ScrollTrigger);
```

Core properties:

| Property | Description |
|---|---|
| `trigger` | The element whose position activates the animation. |
| `start` | e.g. `"top 80%"` — when trigger's top hits 80 % of viewport. |
| `end` | e.g. `"bottom 20%"` — when trigger's bottom hits 20 % of viewport. |
| `scrub` | `true` or number (smoothing) — ties progress to scroll position. |
| `pin` | `true` — pins the trigger element while the animation plays. |
| `toggleActions` | Four states: `onEnter onLeave onEnterBack onLeaveBack`. |

### Stagger

Animate a set of elements with sequential delays:

```js
gsap.from(".card", {
  y: 60,
  opacity: 0,
  duration: 0.5,
  stagger: 0.12,          // 120 ms between each card
  ease: "power2.out",
});
```

Advanced stagger object:

```js
stagger: {
  each: 0.1,
  from: "center",   // "start" | "end" | "center" | "edges" | "random"
  grid: "auto",     // auto-detect grid layout
  ease: "power1.in" // ease the stagger distribution itself
}
```

## Quick Recipes

### Hero Entrance Animation

```js
import gsap from "gsap";

function animateHero() {
  const tl = gsap.timeline({ defaults: { ease: "power3.out" } });

  tl.from(".hero-bg", { scale: 1.2, opacity: 0, duration: 1.2 })
    .from(".hero-title", { y: 60, opacity: 0, duration: 0.8 }, "-=0.6")
    .from(".hero-subtitle", { y: 40, opacity: 0, duration: 0.6 }, "-=0.4")
    .from(".hero-cta", { y: 20, opacity: 0, duration: 0.5 }, "-=0.3");

  return tl; // return so callers can chain or control
}
```

### Sequenced Timeline with Labels

```js
const master = gsap.timeline();

master
  .addLabel("intro")
  .from(".logo", { scale: 0, duration: 0.6, ease: "back.out(1.7)" }, "intro")
  .from(".nav-link", { y: -20, opacity: 0, stagger: 0.08 }, "intro+=0.3")
  .addLabel("content")
  .from(".section-heading", { x: -40, opacity: 0, duration: 0.5 }, "content")
  .from(".section-body", { y: 30, opacity: 0, duration: 0.6 }, "content+=0.2");
```

### Scroll-Scrub Pinned Section

```js
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
gsap.registerPlugin(ScrollTrigger);

gsap.to(".horizontal-panels", {
  xPercent: -100 * (panels.length - 1),
  ease: "none",
  scrollTrigger: {
    trigger: ".horizontal-container",
    pin: true,
    scrub: 1,
    snap: 1 / (panels.length - 1),
    end: () => "+=" + document.querySelector(".horizontal-container").offsetWidth,
  },
});
```

### Scroll-Triggered Fade-In Sections

```js
gsap.utils.toArray(".fade-section").forEach((section) => {
  gsap.from(section, {
    y: 50,
    opacity: 0,
    duration: 0.8,
    ease: "power2.out",
    scrollTrigger: {
      trigger: section,
      start: "top 85%",
      toggleActions: "play none none none",
    },
  });
});
```

### Cleanup Pattern (SPA / React)

```js
import { useEffect, useRef } from "react";
import gsap from "gsap";

function AnimatedComponent() {
  const containerRef = useRef(null);

  useEffect(() => {
    const ctx = gsap.context(() => {
      gsap.from(".box", { x: -100, opacity: 0, stagger: 0.15 });
    }, containerRef); // scope selectors to this container

    return () => ctx.revert(); // kills all tweens created inside
  }, []);

  return (
    <div ref={containerRef}>
      <div className="box">A</div>
      <div className="box">B</div>
    </div>
  );
}
```

## Common Pitfalls

| Pitfall | Fix |
|---|---|
| **Forgetting to register plugins** | Call `gsap.registerPlugin(ScrollTrigger)` (or any plugin) before using it. Unregistered plugins silently do nothing. |
| **Not killing tweens on cleanup** | In SPAs, use `gsap.context()` and call `ctx.revert()` on unmount, or `tl.kill()` manually. Leaked tweens cause memory issues and ghost animations. |
| **Overusing `will-change`** | Only set `will-change` on elements about to animate and remove it after. Applying it everywhere wastes GPU memory. GSAP handles hardware acceleration internally. |
| **Animating layout properties** | Prefer `x`, `y`, `scale`, `rotation`, `opacity` (composited on the GPU) over `width`, `height`, `top`, `left` (trigger layout reflow). |
| **Hard-coded `start`/`end` values** | Use responsive helpers like `"top 80%"` or functional values instead of pixel numbers to avoid scroll breakpoints on different viewports. |
| **Competing CSS transitions** | Remove CSS `transition` on properties GSAP controls; otherwise they fight and cause jank. |

## Best Practices

1. **Set timeline defaults.** Declare shared `duration`, `ease`, and `stagger` in `gsap.timeline({ defaults: {} })` to reduce repetition.
2. **Return timelines from functions.** This lets parent timelines nest them and makes testing easier.
3. **Use `gsap.context()` in component-based frameworks.** It scopes selectors and simplifies cleanup.
4. **Batch ScrollTrigger refreshes.** After dynamic DOM changes call `ScrollTrigger.refresh()` once, not per element.
5. **Prefer relative position parameters** (`"-=0.3"`, `"<"`) over absolute seconds so sequences stay intact when durations change.
6. **Test with `tl.timeScale(0.25)`** to slow down complex sequences during development and catch timing issues.
7. **Respect reduced motion.** Wrap animations in a check:
   ```js
   const prefersReduced = window.matchMedia("(prefers-reduced-motion: reduce)").matches;
   if (!prefersReduced) { animateHero(); }
   ```
8. **Load GSAP from npm** (`npm install gsap`) and tree-shake only the plugins you need to keep bundle size minimal.
