---
name: design-system-patterns
description: Use this skill when building design system architecture, defining token hierarchies, implementing theme switching, or creating variant-based component APIs.
license: Complete terms in LICENSE.txt
---

# Design System Patterns

## When to Use

Apply this skill when the agent needs to:

- Architect a design token hierarchy (primitive → semantic → component)
- Set up CSS custom properties for theming
- Implement light/dark/system theme switching with React context
- Build component variant APIs using class-variance-authority (CVA)
- Configure Style Dictionary for multi-platform token generation
- Establish token naming conventions across a team

## Key Concepts

### Design Token Hierarchy

Design tokens are organized in three layers, each building on the previous:

1. **Primitive tokens** — Raw values with no semantic meaning. These are the palette.
2. **Semantic tokens** — Purpose-driven aliases that reference primitives. These encode intent.
3. **Component tokens** — Scoped overrides for specific components that reference semantic tokens.

```
Primitive:       --color-blue-500: #3b82f6
                          ↓
Semantic:        --color-primary: var(--color-blue-500)
                          ↓
Component:       --button-bg: var(--color-primary)
```

### Token Naming Conventions

Name tokens by **purpose**, never by appearance:

| Bad (visual)       | Good (purpose-based)    |
| ------------------ | ----------------------- |
| `--color-red`      | `--color-danger`        |
| `--font-large`     | `--font-heading`        |
| `--blue-button-bg` | `--button-primary-bg`   |
| `--shadow-dark`    | `--shadow-elevated`     |
| `--spacing-20`     | `--spacing-comfortable` |

Naming pattern: `--{category}-{concept}-{property}-{variant}-{state}`

```
--color-feedback-text-danger-hover
  │       │        │     │      └─ state
  │       │        │     └──────── variant
  │       │        └────────────── property
  │       └─────────────────────── concept
  └─────────────────────────────── category
```

## Patterns

### Primitive Token Layer

Define raw design values as CSS custom properties:

```css
:root {
  /* Colors */
  --color-gray-50: #f9fafb;
  --color-gray-100: #f3f4f6;
  --color-gray-200: #e5e7eb;
  --color-gray-300: #d1d5db;
  --color-gray-400: #9ca3af;
  --color-gray-500: #6b7280;
  --color-gray-600: #4b5563;
  --color-gray-700: #374151;
  --color-gray-800: #1f2937;
  --color-gray-900: #111827;

  --color-blue-50: #eff6ff;
  --color-blue-500: #3b82f6;
  --color-blue-600: #2563eb;
  --color-blue-700: #1d4ed8;

  --color-red-500: #ef4444;
  --color-green-500: #22c55e;

  /* Spacing scale */
  --spacing-1: 0.25rem;
  --spacing-2: 0.5rem;
  --spacing-3: 0.75rem;
  --spacing-4: 1rem;
  --spacing-6: 1.5rem;
  --spacing-8: 2rem;
  --spacing-12: 3rem;
  --spacing-16: 4rem;

  /* Typography scale */
  --font-size-xs: 0.75rem;
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.125rem;
  --font-size-xl: 1.25rem;
  --font-size-2xl: 1.5rem;
  --font-size-3xl: 1.875rem;

  /* Radii */
  --radius-sm: 0.25rem;
  --radius-md: 0.375rem;
  --radius-lg: 0.5rem;
  --radius-xl: 0.75rem;
  --radius-full: 9999px;
}
```

### Semantic Token Layer

Map primitives to purpose-driven tokens:

```css
:root {
  /* Surface */
  --color-bg-primary: var(--color-gray-50);
  --color-bg-secondary: var(--color-gray-100);
  --color-bg-tertiary: var(--color-gray-200);
  --color-bg-inverse: var(--color-gray-900);

  /* Text */
  --color-text-primary: var(--color-gray-900);
  --color-text-secondary: var(--color-gray-600);
  --color-text-muted: var(--color-gray-400);
  --color-text-inverse: var(--color-gray-50);

  /* Interactive */
  --color-interactive-primary: var(--color-blue-500);
  --color-interactive-primary-hover: var(--color-blue-600);
  --color-interactive-primary-active: var(--color-blue-700);

  /* Feedback */
  --color-feedback-danger: var(--color-red-500);
  --color-feedback-success: var(--color-green-500);

  /* Borders */
  --color-border-default: var(--color-gray-200);
  --color-border-strong: var(--color-gray-300);
}
```

### Component Token Layer

Scope tokens to individual components:

```css
:root {
  /* Button component tokens */
  --button-padding-x: var(--spacing-4);
  --button-padding-y: var(--spacing-2);
  --button-radius: var(--radius-md);
  --button-font-size: var(--font-size-sm);

  --button-primary-bg: var(--color-interactive-primary);
  --button-primary-bg-hover: var(--color-interactive-primary-hover);
  --button-primary-text: var(--color-text-inverse);

  /* Card component tokens */
  --card-padding: var(--spacing-6);
  --card-radius: var(--radius-lg);
  --card-bg: var(--color-bg-primary);
  --card-border: var(--color-border-default);
  --card-shadow: 0 1px 3px rgb(0 0 0 / 0.1);
}
```

### Theme Switching with React Context

```tsx
import {
  createContext,
  useContext,
  useEffect,
  useState,
  type ReactNode,
} from "react";

type Theme = "light" | "dark" | "system";

interface ThemeContextValue {
  theme: Theme;
  resolvedTheme: "light" | "dark";
  setTheme: (theme: Theme) => void;
}

const ThemeContext = createContext<ThemeContextValue | undefined>(undefined);

function getSystemTheme(): "light" | "dark" {
  return window.matchMedia("(prefers-color-scheme: dark)").matches ?
      "dark"
    : "light";
}

export function ThemeProvider({
  children,
  defaultTheme = "system",
}: {
  children: ReactNode;
  defaultTheme?: Theme;
}) {
  const [theme, setTheme] = useState<Theme>(() => {
    if (typeof window === "undefined") return defaultTheme;
    return (localStorage.getItem("theme") as Theme) ?? defaultTheme;
  });

  const resolvedTheme = theme === "system" ? getSystemTheme() : theme;

  useEffect(() => {
    const root = document.documentElement;
    root.classList.remove("light", "dark");
    root.classList.add(resolvedTheme);
    root.setAttribute("data-theme", resolvedTheme);
    localStorage.setItem("theme", theme);
  }, [theme, resolvedTheme]);

  useEffect(() => {
    if (theme !== "system") return;
    const mq = window.matchMedia("(prefers-color-scheme: dark)");
    const handler = () => setTheme("system");
    mq.addEventListener("change", handler);
    return () => mq.removeEventListener("change", handler);
  }, [theme]);

  return (
    <ThemeContext.Provider value={{ theme, resolvedTheme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error("useTheme must be used within ThemeProvider");
  return ctx;
}
```

Dark mode overrides via CSS:

```css
.dark {
  --color-bg-primary: var(--color-gray-900);
  --color-bg-secondary: var(--color-gray-800);
  --color-text-primary: var(--color-gray-50);
  --color-text-secondary: var(--color-gray-300);
  --color-border-default: var(--color-gray-700);
  --color-interactive-primary: var(--color-blue-500);
}
```

### CVA Variant System

Use class-variance-authority to build component variant APIs:

```tsx
import { cva, type VariantProps } from "class-variance-authority";
import { cn } from "@/lib/utils";

const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        primary:
          "bg-[var(--button-primary-bg)] text-[var(--button-primary-text)] hover:bg-[var(--button-primary-bg-hover)]",
        secondary:
          "bg-[var(--color-bg-secondary)] text-[var(--color-text-primary)] hover:bg-[var(--color-bg-tertiary)]",
        ghost:
          "hover:bg-[var(--color-bg-secondary)] text-[var(--color-text-primary)]",
        danger:
          "bg-[var(--color-feedback-danger)] text-[var(--color-text-inverse)] hover:opacity-90",
      },
      size: {
        sm: "h-8 px-3 text-xs",
        md: "h-10 px-4 text-sm",
        lg: "h-12 px-6 text-base",
      },
    },
    defaultVariants: {
      variant: "primary",
      size: "md",
    },
  },
);

interface ButtonProps
  extends
    React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}

export function Button({ className, variant, size, ...props }: ButtonProps) {
  return (
    <button
      className={cn(buttonVariants({ variant, size }), className)}
      {...props}
    />
  );
}
```

### Style Dictionary Configuration

Configure Style Dictionary for multi-platform token generation:

```json
{
  "source": ["tokens/**/*.json"],
  "platforms": {
    "css": {
      "transformGroup": "css",
      "buildPath": "build/css/",
      "files": [
        {
          "destination": "tokens.css",
          "format": "css/variables",
          "options": {
            "selector": ":root"
          }
        }
      ]
    },
    "js": {
      "transformGroup": "js",
      "buildPath": "build/js/",
      "files": [
        {
          "destination": "tokens.js",
          "format": "javascript/es6"
        }
      ]
    },
    "ios": {
      "transformGroup": "ios-swift",
      "buildPath": "build/ios/",
      "files": [
        {
          "destination": "DesignTokens.swift",
          "format": "ios-swift/class.swift",
          "className": "DesignTokens"
        }
      ]
    },
    "android": {
      "transformGroup": "android",
      "buildPath": "build/android/",
      "files": [
        {
          "destination": "tokens.xml",
          "format": "android/resources"
        }
      ]
    }
  }
}
```

Token input file (`tokens/color/base.json`):

```json
{
  "color": {
    "blue": {
      "500": { "value": "#3b82f6", "type": "color" },
      "600": { "value": "#2563eb", "type": "color" }
    },
    "primary": {
      "value": "{color.blue.500}",
      "type": "color",
      "comment": "Primary brand color"
    }
  }
}
```

## Common Pitfalls

1. **Naming tokens by visual appearance** — `--color-red` breaks when brand colors change. Use `--color-danger` instead.
2. **Skipping the semantic layer** — Jumping from primitives to components creates tight coupling and makes theming impossible.
3. **Hardcoding values in components** — Every color, spacing, and font size in a component should reference a token. Never use raw hex or pixel values.
4. **One massive token file** — Split tokens by category (color, spacing, typography) and by layer (primitive, semantic, component).
5. **No dark mode from day one** — Retrofitting dark mode is painful. Build the semantic token layer with both themes from the start.
6. **Inconsistent variant APIs** — All components should follow the same variant/size pattern. Use CVA to enforce consistency.
7. **Missing token documentation** — Tokens without descriptions become tribal knowledge. Document purpose and usage for each semantic token.

## Best Practices

- **Name by purpose, not appearance** — `--color-interactive-primary` survives rebrands; `--color-blue` does not.
- **Maintain the three-layer hierarchy** — Primitive → semantic → component. Never skip a layer.
- **Version your tokens** — Token changes are breaking changes. Treat them with the same rigor as API versioning.
- **Automate the token pipeline** — Use Style Dictionary or a similar tool to generate platform-specific outputs from a single source of truth.
- **Provide a cn() utility** — Merge class names cleanly with a utility combining `clsx` and `tailwind-merge`:

```ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

- **Co-locate component tokens** — Define component tokens near the component they serve, not in a global file.
- **Test themes visually** — Use Storybook or a similar tool to render every component in every theme during CI.
- **Enforce token usage via linting** — Use stylelint rules to flag raw color values and require custom property references.
