---
name: interaction-design
description: Use this skill when implementing microinteractions, motion design, state transitions, loading states, gesture interactions, or animation accessibility.
license: Complete terms in LICENSE.txt
---

# Interaction Design

## When to Use

Apply this skill when the agent needs to:

- Add purposeful motion to UI elements (feedback, orientation, focus, continuity)
- Implement loading states with skeleton screens or progress indicators
- Build state transitions with enter/exit animations
- Create gesture-based interactions (swipe, drag)
- Apply CSS keyframe animations and spring-based motion
- Ensure animations respect `prefers-reduced-motion`

## Key Concepts

### Purposeful Motion

Every animation must serve a purpose. Motion without intent is noise.

| Purpose         | What it communicates         | Example                    |
| --------------- | ---------------------------- | -------------------------- |
| **Feedback**    | "Your action was received"   | Button ripple on click     |
| **Orientation** | "Here's where you are"       | Page slide transitions     |
| **Focus**       | "Look at this"               | Pulsing notification badge |
| **Continuity**  | "These elements are related" | Shared element transitions |

### Timing Guidelines

| Category | Duration  | Use case                                 |
| -------- | --------- | ---------------------------------------- |
| Micro    | 100–150ms | Button press, toggle, hover state        |
| Small    | 200–300ms | Tooltips, dropdowns, fade in/out         |
| Medium   | 300–500ms | Modal open/close, panel slide, card flip |
| Complex  | 500ms+    | Page transitions, orchestrated sequences |

Under 100ms feels instant. Over 500ms feels slow. Stay within these ranges.

### Easing Functions

| Easing          | CSS value                           | When to use                        |
| --------------- | ----------------------------------- | ---------------------------------- |
| **ease-out**    | `cubic-bezier(0.0, 0.0, 0.2, 1)`    | Elements entering the screen       |
| **ease-in**     | `cubic-bezier(0.4, 0.0, 1, 1)`      | Elements leaving the screen        |
| **ease-in-out** | `cubic-bezier(0.4, 0.0, 0.2, 1)`    | Elements moving within the screen  |
| **spring**      | `cubic-bezier(0.34, 1.56, 0.64, 1)` | Playful, bouncy interactions       |
| **linear**      | `linear`                            | Progress bars, continuous rotation |

## Patterns

### Accessibility: prefers-reduced-motion

**This must be implemented for every animation.** Users with vestibular disorders can experience nausea, dizziness, or migraines from motion.

```css
/* Global reduced-motion override */
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

React hook for checking motion preference:

```tsx
import { useState, useEffect } from "react";

export function usePrefersReducedMotion(): boolean {
  const [prefersReduced, setPrefersReduced] = useState(() => {
    if (typeof window === "undefined") return false;
    return window.matchMedia("(prefers-reduced-motion: reduce)").matches;
  });

  useEffect(() => {
    const mq = window.matchMedia("(prefers-reduced-motion: reduce)");
    const handler = (event: MediaQueryListEvent) =>
      setPrefersReduced(event.matches);
    mq.addEventListener("change", handler);
    return () => mq.removeEventListener("change", handler);
  }, []);

  return prefersReduced;
}
```

Using in framer-motion:

```tsx
import { motion, useReducedMotion } from "framer-motion";

export function AnimatedCard({ children }: { children: React.ReactNode }) {
  const shouldReduceMotion = useReducedMotion();

  return (
    <motion.div
      initial={shouldReduceMotion ? { opacity: 0 } : { opacity: 0, y: 20 }}
      animate={shouldReduceMotion ? { opacity: 1 } : { opacity: 1, y: 0 }}
      transition={
        shouldReduceMotion ?
          { duration: 0 }
        : { duration: 0.3, ease: "easeOut" }
      }
    >
      {children}
    </motion.div>
  );
}
```

### Loading States: Skeleton Screens

Skeleton screens reduce perceived wait time by showing the layout shape before content arrives:

```tsx
export function Skeleton({ className }: { className?: string }) {
  return (
    <div
      className={cn(
        "animate-pulse rounded-md bg-gray-200 dark:bg-gray-700",
        className,
      )}
      role="status"
      aria-label="Loading"
    />
  );
}

export function CardSkeleton() {
  return (
    <div className="space-y-4 rounded-lg border p-6">
      <Skeleton className="h-4 w-3/4" />
      <Skeleton className="h-4 w-1/2" />
      <div className="space-y-2 pt-4">
        <Skeleton className="h-3 w-full" />
        <Skeleton className="h-3 w-full" />
        <Skeleton className="h-3 w-2/3" />
      </div>
    </div>
  );
}
```

CSS for the pulse animation:

```css
@keyframes pulse {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
}

.animate-pulse {
  animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}

@media (prefers-reduced-motion: reduce) {
  .animate-pulse {
    animation: none;
    opacity: 0.7;
  }
}
```

### Loading States: Progress Bar

```tsx
import { motion } from "framer-motion";

interface ProgressBarProps {
  value: number; // 0-100
  label?: string;
}

export function ProgressBar({ value, label }: ProgressBarProps) {
  const clampedValue = Math.min(100, Math.max(0, value));

  return (
    <div className="space-y-1">
      {label && (
        <div className="flex justify-between text-sm">
          <span>{label}</span>
          <span>{clampedValue}%</span>
        </div>
      )}
      <div
        className="h-2 w-full overflow-hidden rounded-full bg-gray-200 dark:bg-gray-700"
        role="progressbar"
        aria-valuenow={clampedValue}
        aria-valuemin={0}
        aria-valuemax={100}
        aria-label={label}
      >
        <motion.div
          className="h-full rounded-full bg-brand-500"
          initial={{ width: 0 }}
          animate={{ width: `${clampedValue}%` }}
          transition={{ duration: 0.5, ease: "easeOut" }}
        />
      </div>
    </div>
  );
}
```

### State Transitions: Toggle

```tsx
import { motion } from "framer-motion";

interface ToggleProps {
  checked: boolean;
  onChange: (checked: boolean) => void;
  label: string;
}

export function Toggle({ checked, onChange, label }: ToggleProps) {
  return (
    <button
      role="switch"
      aria-checked={checked}
      aria-label={label}
      onClick={() => onChange(!checked)}
      className={cn(
        "relative inline-flex h-6 w-11 shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors",
        checked ? "bg-brand-500" : "bg-gray-300 dark:bg-gray-600",
      )}
    >
      <motion.span
        className="pointer-events-none inline-block h-5 w-5 rounded-full bg-white shadow-sm"
        animate={{ x: checked ? 20 : 0 }}
        transition={{ type: "spring", stiffness: 500, damping: 30 }}
      />
    </button>
  );
}
```

### State Transitions: Page Transitions with AnimatePresence

```tsx
import { AnimatePresence, motion } from "framer-motion";
import { useLocation } from "react-router-dom";

const pageTransition = {
  initial: { opacity: 0, x: 20 },
  animate: { opacity: 1, x: 0 },
  exit: { opacity: 0, x: -20 },
  transition: { duration: 0.3, ease: "easeInOut" },
};

export function PageTransition({ children }: { children: React.ReactNode }) {
  const location = useLocation();

  return (
    <AnimatePresence mode="wait">
      <motion.div
        key={location.pathname}
        initial={pageTransition.initial}
        animate={pageTransition.animate}
        exit={pageTransition.exit}
        transition={pageTransition.transition}
      >
        {children}
      </motion.div>
    </AnimatePresence>
  );
}
```

### Feedback: Ripple Effect

```tsx
import { useState, useCallback, type MouseEvent } from "react";

interface Ripple {
  id: number;
  x: number;
  y: number;
  size: number;
}

export function useRipple() {
  const [ripples, setRipples] = useState<Ripple[]>([]);

  const createRipple = useCallback((event: MouseEvent<HTMLElement>) => {
    const element = event.currentTarget;
    const rect = element.getBoundingClientRect();
    const size = Math.max(rect.width, rect.height) * 2;
    const x = event.clientX - rect.left - size / 2;
    const y = event.clientY - rect.top - size / 2;
    const id = Date.now();

    setRipples((prev) => [...prev, { id, x, y, size }]);
    setTimeout(() => {
      setRipples((prev) => prev.filter((r) => r.id !== id));
    }, 600);
  }, []);

  const RippleContainer = () => (
    <span className="pointer-events-none absolute inset-0 overflow-hidden rounded-[inherit]">
      {ripples.map((ripple) => (
        <span
          key={ripple.id}
          className="absolute animate-ripple rounded-full bg-current opacity-20"
          style={{
            left: ripple.x,
            top: ripple.y,
            width: ripple.size,
            height: ripple.size,
          }}
        />
      ))}
    </span>
  );

  return { createRipple, RippleContainer };
}
```

Ripple CSS:

```css
@keyframes ripple {
  from {
    transform: scale(0);
    opacity: 0.2;
  }
  to {
    transform: scale(1);
    opacity: 0;
  }
}

.animate-ripple {
  animation: ripple 0.6s ease-out forwards;
}
```

### Gesture: Swipe to Dismiss

```tsx
import {
  motion,
  useMotionValue,
  useTransform,
  type PanInfo,
} from "framer-motion";

interface SwipeToDismissProps {
  children: React.ReactNode;
  onDismiss: () => void;
  threshold?: number;
}

export function SwipeToDismiss({
  children,
  onDismiss,
  threshold = 150,
}: SwipeToDismissProps) {
  const x = useMotionValue(0);
  const opacity = useTransform(x, [-threshold, 0, threshold], [0.3, 1, 0.3]);
  const rotateZ = useTransform(x, [-threshold, 0, threshold], [-5, 0, 5]);

  function handleDragEnd(_: unknown, info: PanInfo) {
    if (Math.abs(info.offset.x) > threshold) {
      onDismiss();
    }
  }

  return (
    <motion.div
      drag="x"
      dragConstraints={{ left: 0, right: 0 }}
      dragElastic={0.7}
      onDragEnd={handleDragEnd}
      style={{ x, opacity, rotateZ }}
      whileDrag={{ cursor: "grabbing" }}
    >
      {children}
    </motion.div>
  );
}
```

### CSS Keyframe Animations

```css
/* Fade in and slide up */
@keyframes fade-in-up {
  from {
    opacity: 0;
    transform: translateY(1rem);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in-up {
  animation: fade-in-up 0.3s ease-out both;
}

/* Staggered children */
.stagger-children > * {
  animation: fade-in-up 0.3s ease-out both;
}

.stagger-children > *:nth-child(1) {
  animation-delay: 0ms;
}
.stagger-children > *:nth-child(2) {
  animation-delay: 50ms;
}
.stagger-children > *:nth-child(3) {
  animation-delay: 100ms;
}
.stagger-children > *:nth-child(4) {
  animation-delay: 150ms;
}
.stagger-children > *:nth-child(5) {
  animation-delay: 200ms;
}

/* Spin (for loaders) */
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.animate-spin {
  animation: spin 1s linear infinite;
}

/* Bounce attention */
@keyframes bounce {
  0%,
  100% {
    transform: translateY(0);
    animation-timing-function: cubic-bezier(0.8, 0, 1, 1);
  }
  50% {
    transform: translateY(-25%);
    animation-timing-function: cubic-bezier(0, 0, 0.2, 1);
  }
}

.animate-bounce {
  animation: bounce 1s infinite;
}

/* Reduced motion: remove all custom animations */
@media (prefers-reduced-motion: reduce) {
  .animate-fade-in-up,
  .stagger-children > *,
  .animate-bounce {
    animation: none;
    opacity: 1;
    transform: none;
  }

  .animate-spin {
    animation-duration: 3s;
  }
}
```

### CSS Transitions

```css
/* Reusable transition utilities */
.transition-colors {
  transition-property: color, background-color, border-color;
  transition-duration: 150ms;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
}

.transition-transform {
  transition-property: transform;
  transition-duration: 200ms;
  transition-timing-function: cubic-bezier(0, 0, 0.2, 1);
}

.transition-opacity {
  transition-property: opacity;
  transition-duration: 200ms;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
}

.transition-shadow {
  transition-property: box-shadow;
  transition-duration: 200ms;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
}

/* Hover lift effect */
.hover-lift {
  transition:
    transform 200ms ease-out,
    box-shadow 200ms ease-out;
}

.hover-lift:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgb(0 0 0 / 0.1);
}

@media (prefers-reduced-motion: reduce) {
  .hover-lift:hover {
    transform: none;
  }
}
```

### Orchestrated Stagger with framer-motion

```tsx
import { motion, type Variants } from "framer-motion";

const containerVariants: Variants = {
  hidden: { opacity: 0 },
  visible: {
    opacity: 1,
    transition: {
      staggerChildren: 0.08,
      delayChildren: 0.1,
    },
  },
};

const itemVariants: Variants = {
  hidden: { opacity: 0, y: 20 },
  visible: {
    opacity: 1,
    y: 0,
    transition: { duration: 0.3, ease: "easeOut" },
  },
};

export function StaggeredList({ items }: { items: string[] }) {
  return (
    <motion.ul
      variants={containerVariants}
      initial="hidden"
      animate="visible"
      className="space-y-2"
    >
      {items.map((item, index) => (
        <motion.li
          key={index}
          variants={itemVariants}
          className="rounded-lg border p-4"
        >
          {item}
        </motion.li>
      ))}
    </motion.ul>
  );
}
```

## Common Pitfalls

1. **Animation without purpose** — Every animation must answer "what does this communicate?" If there is no answer, remove it.
2. **Ignoring `prefers-reduced-motion`** — Failing to respect this media query creates accessibility violations. Always provide a reduced-motion fallback.
3. **Too-slow transitions** — Anything over 500ms for a simple interaction feels sluggish. Keep micro-interactions under 200ms.
4. **Uniform easing** — Using the same easing for enter and exit looks unnatural. Use ease-out for entering, ease-in for exiting.
5. **Animating layout properties** — Animating `width`, `height`, `top`, `left` triggers expensive layout recalculations. Animate `transform` and `opacity` instead — they run on the compositor.
6. **Too many simultaneous animations** — More than 3-4 elements animating at once creates visual chaos. Use staggering to sequence motion.
7. **No exit animations** — Elements that appear with animation but vanish instantly feel broken. Use `AnimatePresence` to animate exits.
8. **Ignoring jank** — Test animations at 60fps. Use `will-change: transform` sparingly to promote elements to their own compositor layer.

## Best Practices

- **Respect user preferences** — Always implement `prefers-reduced-motion`. Reduce to opacity-only transitions or instant state changes.
- **Use compositor-friendly properties** — Only animate `transform` and `opacity` for smooth 60fps animation.
- **Match timing to importance** — Micro-interactions: 100–150ms. Modals: 200–300ms. Page transitions: 300–500ms.
- **Exit matches entry** — If an element fades and slides in, it should fade and slide out. Asymmetric enter/exit is disorienting.
- **Use springs for interactive motion** — Spring physics feel more natural than bezier curves for drag, toggle, and gesture responses.
- **Stagger, don't synchronize** — When multiple elements animate, stagger them by 50–100ms for a cascade effect.
- **Skeleton over spinner** — Skeleton screens reduce perceived load time better than spinners because they preview the layout structure.
- **Test at low frame rates** — Throttle CPU in DevTools to ensure animations degrade gracefully on slower devices.
