---
name: responsive-design
description: Use this skill when implementing responsive layouts, fluid typography, container queries, responsive navigation, or adaptive image strategies.
license: Complete terms in LICENSE.txt
---

# Responsive Design

## When to Use

Apply this skill when the agent needs to:

- Implement component-level responsiveness with container queries
- Set up fluid typography that scales without breakpoints
- Build responsive grid layouts with CSS Grid
- Create responsive navigation patterns (hamburger, drawer, tabs)
- Optimize images for multiple screen sizes and densities
- Handle responsive tables on mobile devices
- Use dynamic viewport units for mobile browser chrome

## Key Concepts

### Mobile-First Breakpoint Scale

Design for mobile first, then layer on complexity at larger sizes:

| Breakpoint | Width  | Target                   |
| ---------- | ------ | ------------------------ |
| default    | 0px+   | Mobile phones (portrait) |
| `sm`       | 640px  | Large phones (landscape) |
| `md`       | 768px  | Tablets                  |
| `lg`       | 1024px | Small laptops            |
| `xl`       | 1280px | Desktops                 |
| `2xl`      | 1536px | Large screens            |

Mobile-first means the base styles target the smallest screen. Use `min-width` media queries to progressively enhance.

### Container Queries vs. Media Queries

- **Media queries** respond to the **viewport** width.
- **Container queries** respond to a **parent container's** width.

Container queries enable truly reusable components — a card renders the same whether it's in a sidebar or a full-width layout.

### Dynamic Viewport Units

Mobile browsers have collapsible UI chrome (address bar, toolbars). Standard `vh` uses the largest possible viewport, causing content to hide behind chrome.

| Unit  | Meaning                                  |
| ----- | ---------------------------------------- |
| `dvh` | Dynamic — adjusts as chrome shows/hides  |
| `svh` | Small — viewport with all chrome visible |
| `lvh` | Large — viewport with all chrome hidden  |

Use `dvh` for full-screen layouts on mobile:

```css
.hero {
  min-height: 100dvh;
}
```

## Patterns

### Container Queries

```css
/* Define a containment context */
.card-container {
  container-type: inline-size;
  container-name: card;
}

/* Base: stacked layout */
.card {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}

/* When container is 400px+: side-by-side */
@container card (min-width: 400px) {
  .card {
    grid-template-columns: 200px 1fr;
  }
}

/* When container is 600px+: enhanced layout */
@container card (min-width: 600px) {
  .card {
    grid-template-columns: 250px 1fr auto;
  }

  .card-actions {
    flex-direction: column;
    align-self: center;
  }
}
```

React component with container query:

```tsx
export function CardContainer({
  children,
  className,
}: {
  children: React.ReactNode;
  className?: string;
}) {
  return <div className={cn("@container", className)}>{children}</div>;
}

export function ResponsiveCard({
  title,
  description,
  image,
}: {
  title: string;
  description: string;
  image: string;
}) {
  return (
    <CardContainer>
      <div className="flex flex-col gap-4 @md:flex-row @md:gap-6">
        <img
          src={image}
          alt=""
          className="aspect-video w-full rounded-lg object-cover @md:w-48 @md:aspect-square"
        />
        <div className="flex flex-col gap-2">
          <h3 className="text-lg font-semibold @lg:text-xl">{title}</h3>
          <p className="text-sm text-muted">{description}</p>
        </div>
      </div>
    </CardContainer>
  );
}
```

### Fluid Typography with clamp()

Scale font sizes smoothly between a minimum and maximum, without breakpoints:

```css
:root {
  /* clamp(min, preferred, max) */
  --font-size-heading-1: clamp(2rem, 1.5rem + 2.5vw, 4rem);
  --font-size-heading-2: clamp(1.5rem, 1.2rem + 1.5vw, 2.5rem);
  --font-size-heading-3: clamp(1.25rem, 1.1rem + 0.75vw, 1.75rem);
  --font-size-body: clamp(1rem, 0.95rem + 0.25vw, 1.125rem);
  --font-size-small: clamp(0.8rem, 0.775rem + 0.125vw, 0.875rem);

  /* Fluid spacing */
  --spacing-section: clamp(2rem, 1.5rem + 2.5vw, 6rem);
  --spacing-content: clamp(1rem, 0.75rem + 1.25vw, 2rem);
}

h1 {
  font-size: var(--font-size-heading-1);
}
h2 {
  font-size: var(--font-size-heading-2);
}
h3 {
  font-size: var(--font-size-heading-3);
}
body {
  font-size: var(--font-size-body);
}
small {
  font-size: var(--font-size-small);
}
```

The formula for the preferred value: `preferred = min + (max - min) * (100vw - minViewport) / (maxViewport - minViewport)`.

### CSS Grid Responsive Layouts

**Auto-fit with minimum column width:**

```css
.auto-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 280px), 1fr));
  gap: 1.5rem;
}
```

This creates as many columns as fit, each at least 280px wide, expanding to fill space.

**Named grid areas for layout changes:**

```css
.page-layout {
  display: grid;
  grid-template-areas:
    "header"
    "main"
    "sidebar"
    "footer";
  grid-template-columns: 1fr;
  gap: 1rem;
}

@media (min-width: 768px) {
  .page-layout {
    grid-template-areas:
      "header  header"
      "main    sidebar"
      "footer  footer";
    grid-template-columns: 1fr 300px;
  }
}

@media (min-width: 1280px) {
  .page-layout {
    grid-template-areas:
      "header  header  header"
      "nav     main    sidebar"
      "footer  footer  footer";
    grid-template-columns: 220px 1fr 320px;
  }
}

.header {
  grid-area: header;
}
.main {
  grid-area: main;
}
.sidebar {
  grid-area: sidebar;
}
.nav {
  grid-area: nav;
}
.footer {
  grid-area: footer;
}
```

### Responsive Navigation

**Mobile hamburger → desktop horizontal nav:**

```tsx
import { useState } from "react";

export function Navigation({
  items,
}: {
  items: { label: string; href: string }[];
}) {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <nav className="relative">
      {/* Mobile toggle */}
      <button
        className="p-2 lg:hidden"
        onClick={() => setIsOpen(!isOpen)}
        aria-expanded={isOpen}
        aria-label="Toggle navigation"
      >
        <span className="sr-only">Menu</span>
        {isOpen ?
          <svg
            className="h-6 w-6"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth={2}
              d="M6 18L18 6M6 6l12 12"
            />
          </svg>
        : <svg
            className="h-6 w-6"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth={2}
              d="M4 6h16M4 12h16M4 18h16"
            />
          </svg>
        }
      </button>

      {/* Navigation links */}
      <ul
        className={`
          ${isOpen ? "flex" : "hidden"}
          absolute left-0 right-0 top-full flex-col gap-1 bg-white p-4 shadow-lg
          lg:relative lg:flex lg:flex-row lg:gap-6 lg:bg-transparent lg:p-0 lg:shadow-none
        `}
      >
        {items.map((item) => (
          <li key={item.href}>
            <a
              href={item.href}
              className="block rounded-md px-3 py-2 text-sm font-medium hover:bg-gray-100 lg:hover:bg-transparent lg:hover:text-brand-500"
            >
              {item.label}
            </a>
          </li>
        ))}
      </ul>
    </nav>
  );
}
```

### Responsive Images

**Using `<picture>` for art direction:**

```html
<picture>
  <!-- Large screens: wide landscape crop -->
  <source
    media="(min-width: 1024px)"
    srcset="hero-desktop.avif 1x, hero-desktop-2x.avif 2x"
    type="image/avif"
  />
  <source
    media="(min-width: 1024px)"
    srcset="hero-desktop.webp 1x, hero-desktop-2x.webp 2x"
    type="image/webp"
  />

  <!-- Tablets: taller crop -->
  <source
    media="(min-width: 640px)"
    srcset="hero-tablet.avif"
    type="image/avif"
  />

  <!-- Mobile: square or portrait crop (default) -->
  <img
    src="hero-mobile.jpg"
    srcset="hero-mobile.avif"
    alt="Hero image"
    class="w-full h-auto object-cover"
    loading="lazy"
    decoding="async"
    width="800"
    height="600"
  />
</picture>
```

**Using `srcset` with size descriptors:**

```html
<img
  src="photo-800.jpg"
  srcset="
    photo-400.jpg   400w,
    photo-800.jpg   800w,
    photo-1200.jpg 1200w,
    photo-1600.jpg 1600w
  "
  sizes="
    (min-width: 1280px) 33vw,
    (min-width: 768px) 50vw,
    100vw
  "
  alt="Responsive photo"
  loading="lazy"
  decoding="async"
/>
```

### Responsive Tables

**Approach 1: Horizontal scroll**

```css
.table-scroll-wrapper {
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
  border: 1px solid var(--color-border);
  border-radius: 0.5rem;
}

.table-scroll-wrapper table {
  min-width: 600px;
  width: 100%;
}
```

**Approach 2: Card-based mobile layout**

```css
/* Desktop: standard table */
.responsive-table thead {
  display: table-header-group;
}
.responsive-table tr {
  display: table-row;
}
.responsive-table td {
  display: table-cell;
}

/* Mobile: each row becomes a card */
@media (max-width: 767px) {
  .responsive-table thead {
    display: none;
  }

  .responsive-table tr {
    display: block;
    margin-bottom: 1rem;
    border: 1px solid var(--color-border);
    border-radius: 0.5rem;
    padding: 1rem;
  }

  .responsive-table td {
    display: flex;
    justify-content: space-between;
    padding: 0.25rem 0;
    border: none;
  }

  .responsive-table td::before {
    content: attr(data-label);
    font-weight: 600;
    margin-right: 1rem;
  }
}
```

```html
<table class="responsive-table">
  <thead>
    <tr>
      <th>Name</th>
      <th>Role</th>
      <th>Status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-label="Name">Alice</td>
      <td data-label="Role">Engineer</td>
      <td data-label="Status">Active</td>
    </tr>
  </tbody>
</table>
```

### Dynamic Viewport Units in Practice

```css
/* Full-screen hero */
.hero {
  min-height: 100dvh;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Fixed bottom bar — account for mobile chrome */
.bottom-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  padding-bottom: env(safe-area-inset-bottom);
}

/* Sticky header with remaining viewport */
.main-content {
  height: calc(100dvh - var(--header-height));
  overflow-y: auto;
}
```

## Common Pitfalls

1. **Desktop-first styling** — Starting with desktop and overriding down creates more CSS and more bugs. Always start mobile-first with `min-width` queries.
2. **Using `vh` for full-screen on mobile** — Standard `vh` ignores browser chrome on mobile. Use `dvh` for dynamic adjustment or `svh` for the safe minimum.
3. **Fixed-width containers** — Never use pixel-width containers. Use `max-width` with percentage-based padding.
4. **Forgetting `min()` in `minmax()`** — `grid-template-columns: repeat(auto-fit, minmax(280px, 1fr))` breaks on narrow viewports. Use `minmax(min(100%, 280px), 1fr)`.
5. **Images without `width`/`height`** — Omitting intrinsic dimensions causes layout shift. Always specify `width` and `height` attributes.
6. **Ignoring touch targets** — Interactive elements must be at least 44×44px on touch devices. Small buttons and links frustrate mobile users.
7. **Media queries for component layout** — If a component needs to respond to its own container size rather than the viewport, use `@container` queries instead.
8. **Breakpoints based on devices** — Set breakpoints where content breaks, not at arbitrary device widths.

## Best Practices

- **Mobile-first always** — Write base styles for mobile, then enhance with `min-width` media queries at each breakpoint.
- **Content-driven breakpoints** — Add breakpoints where the layout breaks, not at predetermined device widths.
- **Fluid over fixed** — Prefer `clamp()`, `vw`, and percentage-based values over fixed pixel sizes.
- **Touch targets ≥ 44px** — All interactive elements should have a minimum tap area of 44×44 CSS pixels, per WCAG 2.5.8.
- **Test on real devices** — Emulators miss touch behavior, scroll inertia, and browser chrome quirks.
- **Responsive images by default** — Always use `srcset`/`sizes` or `<picture>`. Serving 2000px images to a 375px phone wastes bandwidth.
- **Use logical properties** — Prefer `margin-inline`, `padding-block`, `inline-size` over direction-specific properties for internationalization.
- **Avoid hiding content on mobile** — Don't use `display: none` to hide entire sections. Rethink the layout instead of removing content.
