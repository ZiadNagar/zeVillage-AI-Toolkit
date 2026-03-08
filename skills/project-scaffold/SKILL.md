---
name: project-scaffold
description: Use this skill when the user asks to bootstrap a new project, initialize a codebase, create a starter template, set up a new app, scaffold a component library, or generate project boilerplate. Covers Next.js, React, Vite, Astro, Remix, and static sites with TypeScript, Tailwind CSS, ESLint, Prettier, testing setup, and CI/CD config. Also triggers for "start a new project", "set up a monorepo", "create a design system", "init a CLI tool", or "generate a project structure".
license: Complete terms in LICENSE.txt
---

# Project Scaffold

You are bootstrapping a new project. Follow these steps to create a well-structured, production-ready foundation.

## Process

### Step 1: Gather Requirements

Before generating anything, determine:

- **Type:** Web app, component library, CLI tool, API, static site, monorepo?
- **Framework:** Next.js, Vite + React, Astro, Remix, plain HTML/CSS/JS?
- **Styling:** Tailwind CSS, CSS Modules, Styled Components, vanilla CSS?
- **Testing:** Vitest, Jest, Playwright?
- **Language:** TypeScript (strongly recommended) or JavaScript?

If the user doesn't specify, use this default stack:

- **Next.js 15+** with App Router
- **TypeScript** (strict mode)
- **Tailwind CSS 4+**
- **Vitest** for unit/component tests
- **Playwright** for E2E
- **ESLint + Prettier** for formatting
- **pnpm** as package manager

### Step 2: Generate Structure

#### Web App (Next.js)

```
project-name/
├── .github/
│   └── workflows/
│       └── ci.yml
├── public/
│   ├── favicon.ico
│   └── og-image.png
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── globals.css
│   │   └── (routes)/
│   ├── components/
│   │   ├── ui/              # Atomic UI components
│   │   └── layout/          # Layout components
│   ├── hooks/               # Custom React hooks
│   ├── lib/                 # Utility functions
│   ├── types/               # Shared TypeScript types
│   └── styles/              # Design tokens, global styles
├── tests/
│   ├── e2e/                 # Playwright E2E tests
│   └── setup.ts             # Test setup file
├── .env.example
├── .eslintrc.cjs
├── .prettierrc
├── .gitignore
├── next.config.ts
├── package.json
├── playwright.config.ts
├── tailwind.config.ts
├── tsconfig.json
└── vitest.config.ts
```

#### Component Library

```
design-system/
├── src/
│   ├── components/
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.test.tsx
│   │   │   ├── Button.stories.tsx
│   │   │   └── index.ts
│   │   └── index.ts         # Barrel export
│   ├── tokens/
│   │   ├── colors.ts
│   │   ├── spacing.ts
│   │   ├── typography.ts
│   │   └── motion.ts        # Animation tokens
│   ├── hooks/
│   └── index.ts              # Package entry point
├── .storybook/
├── package.json
├── tsconfig.json
├── tsup.config.ts             # Build config
└── vitest.config.ts
```

### Step 3: Essential Config Files

#### TypeScript (strict)

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "forceConsistentCasingInFileNames": true,
    "moduleResolution": "bundler",
    "module": "ESNext",
    "target": "ES2022",
    "jsx": "react-jsx",
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

#### ESLint

```javascript
module.exports = {
  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react-hooks/recommended",
    "plugin:jsx-a11y/recommended",
    "prettier",
  ],
  rules: {
    "no-console": ["warn", { allow: ["warn", "error"] }],
    "@typescript-eslint/no-unused-vars": ["error", { argsIgnorePattern: "^_" }],
    "react-hooks/exhaustive-deps": "error",
  },
};
```

#### .gitignore

```
node_modules/
.next/
dist/
build/
out/
coverage/
*.tsbuildinfo
.env
.env.local
.DS_Store
*.log
```

#### .env.example

```bash
# Copy to .env.local and fill in values
NEXT_PUBLIC_SITE_URL=http://localhost:3000
DATABASE_URL=
API_KEY=
```

### Step 4: Package.json Scripts

```json
{
  "scripts": {
    "dev": "next dev --turbo",
    "build": "next build",
    "start": "next start",
    "lint": "eslint src/ --ext .ts,.tsx",
    "lint:fix": "eslint src/ --ext .ts,.tsx --fix",
    "format": "prettier --write .",
    "type-check": "tsc --noEmit",
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "prepare": "husky"
  }
}
```

### Step 5: CI/CD (GitHub Actions)

```yaml
# .github/workflows/ci.yml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: pnpm
      - run: pnpm install --frozen-lockfile
      - run: pnpm lint
      - run: pnpm type-check
      - run: pnpm test --run
      - run: pnpm build
```

## Design Tokens Template

For frontend/creative projects, always include a tokens file:

```typescript
// src/styles/tokens.ts
export const tokens = {
  color: {
    primary: { 50: "#eff6ff", 500: "#3b82f6", 900: "#1e3a8a" },
    neutral: { 50: "#fafafa", 500: "#737373", 900: "#171717" },
    success: "#22c55e",
    warning: "#f59e0b",
    error: "#ef4444",
  },
  spacing: {
    xs: "0.25rem",
    sm: "0.5rem",
    md: "1rem",
    lg: "1.5rem",
    xl: "2rem",
  },
  radius: { sm: "0.25rem", md: "0.5rem", lg: "1rem", full: "9999px" },
  shadow: {
    sm: "0 1px 2px rgb(0 0 0 / 0.05)",
    md: "0 4px 6px rgb(0 0 0 / 0.1)",
    lg: "0 10px 15px rgb(0 0 0 / 0.1)",
  },
  motion: {
    duration: { fast: "150ms", normal: "300ms", slow: "500ms" },
    easing: {
      default: "cubic-bezier(0.4, 0, 0.2, 1)",
      spring: "cubic-bezier(0.34, 1.56, 0.64, 1)",
      bounce: "cubic-bezier(0.68, -0.55, 0.27, 1.55)",
    },
  },
} as const;
```

## Rules

- Always use TypeScript in strict mode
- Always include an `.env.example` — never commit actual `.env` files
- Always add a `README.md` with setup instructions
- Path aliases (`@/`) must be configured in both `tsconfig.json` and the bundler
- Include `prefers-reduced-motion` handling in any animation setup
- Set up git hooks (husky + lint-staged) for pre-commit quality checks
