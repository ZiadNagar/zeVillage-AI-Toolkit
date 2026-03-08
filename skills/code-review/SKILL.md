---
name: code-review
description: Use this skill when the user asks to review code, audit a file or PR, find bugs, check for security vulnerabilities, analyze performance bottlenecks, or improve code quality. Also triggers for requests like "what's wrong with this code", "is this secure", "review my component", or "check for accessibility issues". Covers frontend-focused reviews including React/Vue/Svelte component patterns, CSS/animation performance, accessibility (a11y), bundle size impact, and browser compatibility. Produces structured, actionable feedback with severity levels and fix suggestions.
license: Complete terms in LICENSE.txt
---

# Code Review

You are performing a structured code review. Follow this process for every review.

## Review Process

### Step 1: Understand Scope

Before reviewing, determine:

- What files/components are being reviewed?
- Is this a full review or focused (security, performance, accessibility)?
- What framework/stack is the code using?

### Step 2: Multi-Pass Analysis

Run these passes in order. Skip passes the user explicitly excludes.

#### Pass 1 — Correctness & Bugs

- Logic errors, off-by-one, null/undefined access
- Race conditions in async code
- Missing error boundaries in React/component trees
- Incorrect hook dependencies (`useEffect`, `useMemo`, `useCallback`)
- State mutations that bypass reactivity (direct array/object mutation)

#### Pass 2 — Security

- XSS via `dangerouslySetInnerHTML`, `v-html`, or unescaped template interpolation
- Exposed API keys, tokens, or secrets in client-side code
- Insecure `postMessage` usage without origin checks
- Open redirect vulnerabilities
- Missing CSRF protections on form submissions
- Unsafe `eval()`, `Function()`, or dynamic `import()` from user input

#### Pass 3 — Performance

- Unnecessary re-renders (missing memoization, unstable references)
- Layout thrashing (reading + writing DOM in loops)
- Expensive CSS: `filter`, `backdrop-filter`, `box-shadow` on animated elements
- `will-change` overuse or missing for animated elements
- Large bundle imports that could be lazy-loaded or tree-shaken
- Images without `loading="lazy"`, missing `srcset`/`sizes`
- Animations using `top/left` instead of `transform`

#### Pass 4 — Accessibility (a11y)

- Missing `alt` text, `aria-label`, or `role` attributes
- Non-semantic HTML (div soup)
- Color contrast issues (reference WCAG 2.1 AA)
- Focus management: can the UI be navigated by keyboard?
- Motion: does the code respect `prefers-reduced-motion`?
- Touch targets under 44x44px

#### Pass 5 — Code Quality & Style

- Dead code, unused imports, unreachable branches
- Overly complex functions (consider cyclomatic complexity > 10)
- Magic numbers/strings without named constants
- Naming: unclear abbreviations, misleading names
- File length > 300 lines — suggest splitting
- Missing TypeScript types or `any` abuse

### Step 3: Structured Output

Present findings in this format:

```
## Review Summary

**Files reviewed:** [list]
**Overall:** [PASS | NEEDS WORK | CRITICAL ISSUES]

### Critical 🔴
- [Finding with file:line reference and fix suggestion]

### Warning 🟡
- [Finding with explanation and fix suggestion]

### Suggestion 🟢
- [Nice-to-have improvement]

### Positive ✅
- [Things done well — always include at least one]
```

## Rules

- Always include at least one positive finding. Reviews should be constructive.
- Provide a concrete fix or code snippet for every Critical and Warning finding.
- Don't flag style preferences that are purely subjective (tabs vs spaces, semicolons, etc.) unless they violate the project's existing conventions.
- For performance findings, explain the _why_ — e.g., "this causes layout recalculation because..."
- When reviewing animations/transitions, check for `transform`/`opacity`-only animations on the compositor thread.
- If the codebase uses a specific linter/formatter config, defer to it for style issues.

## Frontend-Specific Checklist

When reviewing frontend code, also check:

- [ ] Components have clear prop types / interfaces
- [ ] Event handlers are properly cleaned up (removeEventListener, abort controllers)
- [ ] CSS animations use GPU-accelerated properties (`transform`, `opacity`)
- [ ] `z-index` values follow a documented scale (not random large numbers)
- [ ] Responsive design handles common breakpoints
- [ ] Loading/error/empty states are handled
- [ ] Text is not hardcoded (i18n-ready if applicable)
