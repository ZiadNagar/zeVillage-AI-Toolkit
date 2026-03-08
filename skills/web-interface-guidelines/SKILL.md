---
name: web-interface-guidelines
description: "Use this skill when building polished, accessible web interfaces — interaction patterns, animation guidelines, layout systems, form design, dark mode, typography scales, color systems, and Core Web Vitals optimization."
license: Complete terms in LICENSE.txt
---

# Web Interface Guidelines

## When to Use

- Designing interaction patterns: hover states, transitions, focus management, keyboard navigation.
- Establishing animation durations, easing curves, and reduced-motion support.
- Building a consistent spacing, grid, and responsive breakpoint system.
- Writing microcopy, error messages, and empty-state content.
- Implementing dark mode, color systems, and typography scales.
- Optimizing for Core Web Vitals and accessibility compliance.

## Interaction Patterns

### Hover States

Provide visual feedback on every interactive element. Keep hover transitions fast (150–200ms).

```css
/* Base interactive element */
.interactive {
  transition:
    background-color 150ms ease,
    color 150ms ease,
    box-shadow 150ms ease;
}

.interactive:hover {
  background-color: var(--hover-bg);
}

/* Subtle lift on card hover */
.card {
  transition:
    transform 200ms ease,
    box-shadow 200ms ease;
}

.card:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgb(0 0 0 / 0.08);
}
```

### Focus Management

- Every focusable element must have a visible focus indicator.
- Use `focus-visible` to show outlines only for keyboard users.
- Manage focus programmatically when opening modals or drawers.

```css
/* Visible keyboard focus ring */
:focus-visible {
  outline: 2px solid var(--focus-ring);
  outline-offset: 2px;
}

/* Remove default outline only when focus-visible is supported */
:focus:not(:focus-visible) {
  outline: none;
}
```

### Keyboard Navigation

| Key                 | Expected Behavior                                           |
| ------------------- | ----------------------------------------------------------- |
| `Tab` / `Shift+Tab` | Move focus forward / backward through focusable elements.   |
| `Enter` / `Space`   | Activate buttons, links, and toggles.                       |
| `Escape`            | Close modals, dropdowns, popovers. Return focus to trigger. |
| `Arrow keys`        | Navigate within composite widgets (tabs, menus, listboxes). |
| `Home` / `End`      | Jump to first / last item in a list or menu.                |

```js
// Trap focus inside a modal
function trapFocus(modal) {
  const focusable = modal.querySelectorAll(
    'a[href], button:not([disabled]), input:not([disabled]), select, textarea, [tabindex]:not([tabindex="-1"])',
  );
  const first = focusable[0];
  const last = focusable[focusable.length - 1];

  modal.addEventListener("keydown", (e) => {
    if (e.key !== "Tab") return;
    if (e.shiftKey && document.activeElement === first) {
      e.preventDefault();
      last.focus();
    } else if (!e.shiftKey && document.activeElement === last) {
      e.preventDefault();
      first.focus();
    }
  });

  first?.focus();
}
```

## Animation Guidelines

### Duration Scale

| Token                 | Duration | Use Case                            |
| --------------------- | -------- | ----------------------------------- |
| `--duration-instant`  | 100ms    | Checkboxes, toggles, color changes. |
| `--duration-fast`     | 150ms    | Hover states, button presses.       |
| `--duration-normal`   | 200ms    | Dropdowns, tooltips, small reveals. |
| `--duration-moderate` | 300ms    | Modals, drawers, accordions.        |
| `--duration-slow`     | 500ms    | Page transitions, large reveals.    |

### Easing Functions

| Token            | Value                               | Use Case                             |
| ---------------- | ----------------------------------- | ------------------------------------ |
| `--ease-default` | `cubic-bezier(0.25, 0.1, 0.25, 1)`  | General-purpose transitions.         |
| `--ease-in`      | `cubic-bezier(0.4, 0, 1, 1)`        | Elements exiting the viewport.       |
| `--ease-out`     | `cubic-bezier(0, 0, 0.2, 1)`        | Elements entering the viewport.      |
| `--ease-in-out`  | `cubic-bezier(0.4, 0, 0.2, 1)`      | Elements moving within the viewport. |
| `--ease-spring`  | `cubic-bezier(0.34, 1.56, 0.64, 1)` | Playful, bouncy interactions.        |

### Reduced Motion

Always respect the user's motion preferences.

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

In JavaScript, check before triggering animations:

```js
const prefersReducedMotion = window.matchMedia(
  "(prefers-reduced-motion: reduce)",
).matches;

if (!prefersReducedMotion) {
  element.animate([{ opacity: 0 }, { opacity: 1 }], { duration: 300 });
} else {
  element.style.opacity = 1;
}
```

## Layout Principles

### Spacing System

Use a consistent 4px grid. Define tokens for common spacing values.

| Token        | Value | Usage                              |
| ------------ | ----- | ---------------------------------- |
| `--space-1`  | 4px   | Inline icon gap.                   |
| `--space-2`  | 8px   | Compact list items, input padding. |
| `--space-3`  | 12px  | Card inner padding (compact).      |
| `--space-4`  | 16px  | Default gap between elements.      |
| `--space-6`  | 24px  | Section padding, card padding.     |
| `--space-8`  | 32px  | Between content groups.            |
| `--space-12` | 48px  | Between major sections.            |
| `--space-16` | 64px  | Page section gaps (mobile).        |
| `--space-24` | 96px  | Page section gaps (desktop).       |

### Grid Usage

```css
/* Content container */
.container {
  max-width: 1280px;
  margin-inline: auto;
  padding-inline: var(--space-6);
}

/* Responsive grid */
.grid-auto {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(100%, 300px), 1fr));
  gap: var(--space-6);
}
```

### Responsive Breakpoints

| Breakpoint | Width  | Typical Layout                             |
| ---------- | ------ | ------------------------------------------ |
| `sm`       | 640px  | Single column, larger touch targets.       |
| `md`       | 768px  | Two-column layouts begin.                  |
| `lg`       | 1024px | Three-column layouts, sidebars.            |
| `xl`       | 1280px | Max content width, wider grids.            |
| `2xl`      | 1536px | Extended content or centered with padding. |

Design mobile-first: start with the smallest screen and layer on complexity.

## Content Guidelines

### Writing Style

- **Concise**: lead with the action or outcome, cut filler words.
- **Active voice**: "Save your changes" not "Changes will be saved."
- **Sentence case**: capitalize only the first word and proper nouns in headings and labels.
- **No jargon**: prefer "Remove" over "Revoke" and "Fix" over "Remediate."

### Microcopy

| Element       | Good                                        | Bad                                         |
| ------------- | ------------------------------------------- | ------------------------------------------- |
| Button        | Save changes                                | Submit                                      |
| Empty state   | No projects yet. Create one to get started. | No data available.                          |
| Loading       | Loading your dashboard...                   | Please wait.                                |
| Success toast | Project created                             | Your project has been successfully created. |

### Error Messages

Structure: **What happened** + **Why** (if useful) + **What to do next**.

```
✗  Could not save — the file name contains invalid characters. Rename and try again.
✗  Connection lost. Check your internet connection and reload the page.
✗  Email already in use. Sign in instead or use a different address.
```

### Empty States

Every empty state should include:

1. An illustration or icon (optional but helpful).
2. A short explanation of what will appear here.
3. A primary action to populate the area.

```html
<div class="flex flex-col items-center py-16 text-center">
  <svg
    class="h-12 w-12 text-gray-300 dark:text-gray-600"
    fill="none"
    viewBox="0 0 24 24"
    stroke-width="1"
    stroke="currentColor"
  >
    <path
      stroke-linecap="round"
      stroke-linejoin="round"
      d="M12 10.5v6m3-3H9m4.06-7.19l-2.12-2.12a1.5 1.5 0 00-1.061-.44H4.5A2.25 2.25 0 002.25 6v12a2.25 2.25 0 002.25 2.25h15A2.25 2.25 0 0021.75 18V9a2.25 2.25 0 00-2.25-2.25h-5.379a1.5 1.5 0 01-1.06-.44z"
    />
  </svg>
  <h3 class="mt-4 text-sm font-semibold text-gray-900 dark:text-white">
    No projects
  </h3>
  <p class="mt-1 text-sm text-gray-500 dark:text-gray-400">
    Get started by creating a new project.
  </p>
  <button
    type="button"
    class="mt-6 rounded-lg bg-indigo-600 px-4 py-2 text-sm font-semibold text-white hover:bg-indigo-500 transition-colors"
  >
    New project
  </button>
</div>
```

## Form Design

### Input Patterns

```html
<div>
  <label
    for="email"
    class="block text-sm font-medium text-gray-700 dark:text-gray-300"
  >
    Email address
  </label>
  <input
    type="email"
    id="email"
    name="email"
    autocomplete="email"
    required
    placeholder="you@example.com"
    class="mt-1.5 block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm text-gray-900 shadow-sm placeholder:text-gray-400 focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 dark:border-gray-700 dark:bg-gray-800 dark:text-white dark:placeholder:text-gray-500"
    aria-describedby="email-hint"
  />
  <p id="email-hint" class="mt-1.5 text-xs text-gray-500 dark:text-gray-400">
    We will never share your email.
  </p>
</div>
```

### Validation

- Validate on blur, not on every keystroke.
- Show inline errors immediately below the input.
- Use `aria-invalid="true"` and `aria-describedby` for screen readers.
- Mark required fields with `<span aria-hidden="true">*</span>` and a legend.

```html
<!-- Error state -->
<input
  type="email"
  aria-invalid="true"
  aria-describedby="email-error"
  class="... border-red-500 focus:border-red-500 focus:ring-red-500"
/>
<p
  id="email-error"
  role="alert"
  class="mt-1.5 text-xs text-red-600 dark:text-red-400"
>
  Enter a valid email address.
</p>
```

### Form Accessibility

- Every input must have a visible `<label>`.
- Group related fields with `<fieldset>` and `<legend>`.
- Use `autocomplete` attributes for common fields (name, email, address).
- Ensure error messages are announced: `role="alert"` or `aria-live="assertive"`.

## Performance

### Core Web Vitals Targets

| Metric                              | Good    | Needs Improvement | Poor    |
| ----------------------------------- | ------- | ----------------- | ------- |
| **LCP** (Largest Contentful Paint)  | ≤ 2.5s  | 2.5–4.0s          | > 4.0s  |
| **INP** (Interaction to Next Paint) | ≤ 200ms | 200–500ms         | > 500ms |
| **CLS** (Cumulative Layout Shift)   | ≤ 0.1   | 0.1–0.25          | > 0.25  |

### Optimization Techniques

- **Lazy loading**: defer images and iframes below the fold with `loading="lazy"`.
- **Image optimization**: serve WebP/AVIF, use `srcset`/`sizes`, set explicit `width`/`height`.
- **Font loading**: self-host fonts, use `font-display: swap`, subset to required characters.
- **Code splitting**: load JavaScript for each route on demand.
- **Resource hints**: `<link rel="preconnect">` for third-party origins, `<link rel="preload">` for critical assets.
- **Minimize CLS**: set dimensions on images, videos, and ads; avoid inserting content above existing content.

## Visual Design

### Color System

Define a palette using CSS custom properties. Include semantic tokens for surfaces, text, and interactive states.

```css
:root {
  /* Neutral scale */
  --gray-50: #fafafa;
  --gray-100: #f4f4f5;
  --gray-200: #e4e4e7;
  --gray-300: #d4d4d8;
  --gray-400: #a1a1aa;
  --gray-500: #71717a;
  --gray-600: #52525b;
  --gray-700: #3f3f46;
  --gray-800: #27272a;
  --gray-900: #18181b;
  --gray-950: #09090b;

  /* Brand */
  --brand-500: #6366f1;
  --brand-600: #4f46e5;

  /* Semantic tokens */
  --color-bg: var(--gray-50);
  --color-bg-subtle: white;
  --color-text: var(--gray-900);
  --color-text-muted: var(--gray-500);
  --color-border: var(--gray-200);
  --color-focus-ring: var(--brand-500);
}

.dark {
  --color-bg: var(--gray-950);
  --color-bg-subtle: var(--gray-900);
  --color-text: var(--gray-50);
  --color-text-muted: var(--gray-400);
  --color-border: var(--gray-800);
}
```

### Typography Scale

Use a modular scale (1.25 ratio) for consistent sizing.

| Token         | Size | Line Height | Use                      |
| ------------- | ---- | ----------- | ------------------------ |
| `--text-xs`   | 12px | 16px        | Captions, badges.        |
| `--text-sm`   | 14px | 20px        | Body small, table cells. |
| `--text-base` | 16px | 24px        | Default body text.       |
| `--text-lg`   | 18px | 28px        | Section intros.          |
| `--text-xl`   | 20px | 28px        | Card headings.           |
| `--text-2xl`  | 24px | 32px        | Section headings.        |
| `--text-3xl`  | 30px | 36px        | Page headings.           |
| `--text-4xl`  | 36px | 40px        | Hero subtext.            |
| `--text-5xl`  | 48px | 48px        | Hero headline.           |

### Iconography

- Use a single icon library consistently (e.g., Heroicons, Lucide).
- Icons should be 20×20 (inline) or 24×24 (standalone) by default.
- Always pair icons with visible text labels or `aria-label`.
- Decorative icons use `aria-hidden="true"`.

### Elevation & Shadows

| Level | Shadow                              | Use                      |
| ----- | ----------------------------------- | ------------------------ |
| 0     | none                                | Flat elements, embedded. |
| 1     | `0 1px 2px rgb(0 0 0 / 0.05)`       | Cards, inputs.           |
| 2     | `0 4px 6px -1px rgb(0 0 0 / 0.1)`   | Dropdowns, popovers.     |
| 3     | `0 10px 15px -3px rgb(0 0 0 / 0.1)` | Modals, dialogs.         |
| 4     | `0 20px 25px -5px rgb(0 0 0 / 0.1)` | Floating panels, toasts. |

## Dark Mode Implementation

### CSS-Based Toggle

```css
/* System preference */
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg: var(--gray-950);
    --color-text: var(--gray-50);
    /* ...other dark tokens */
  }
}

/* Class-based override (user toggle) */
.dark {
  --color-bg: var(--gray-950);
  --color-text: var(--gray-50);
}
```

### JavaScript Toggle

```js
function setTheme(theme) {
  const root = document.documentElement;
  if (theme === "dark") {
    root.classList.add("dark");
  } else {
    root.classList.remove("dark");
  }
  localStorage.setItem("theme", theme);
}

// Initialize on load
const saved = localStorage.getItem("theme");
const systemDark = window.matchMedia("(prefers-color-scheme: dark)").matches;
setTheme(saved || (systemDark ? "dark" : "light"));
```

### Dark Mode Best Practices

- Never invert colors naively — reduce contrast slightly (use gray-50 on gray-950, not white on black).
- Reduce shadow intensity in dark mode; use subtle borders instead.
- Test images, illustrations, and brand colors in both modes.
- Provide a three-way toggle: Light / Dark / System.

## Anti-Patterns

- **Missing focus styles** — never remove focus outlines without providing a visible alternative.
- **Animations without reduced-motion** — always implement `prefers-reduced-motion`.
- **Inconsistent spacing** — random padding/margin values destroy visual rhythm.
- **Placeholder-only labels** — placeholders disappear on input; always use a visible label.
- **Color-only indicators** — never communicate state (error, success) by color alone.
- **Layout shift on load** — explicitly size images and dynamic content containers.
- **Auto-dismissing errors** — keep error messages visible until the user corrects the issue.
- **Nested scroll containers** — avoid scrollable areas inside scrollable areas.
