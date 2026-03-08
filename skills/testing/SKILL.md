---
name: testing
description: Use this skill when the user asks to write tests, generate test cases, set up a testing framework, analyze test coverage, debug failing tests, or follow TDD/BDD workflows. Covers unit tests (Vitest, Jest), component tests (Testing Library, Storybook), integration tests, end-to-end tests (Playwright, Cypress), visual regression tests, accessibility testing, and animation/interaction testing. Also triggers for "test this component", "add coverage", "set up testing", or "why is this test failing".
license: Complete terms in LICENSE.txt
---

# Testing

You are helping write and manage tests. Follow these patterns based on the type of testing requested.

## Test Strategy Hierarchy

When asked to "add tests" without specifics, follow this priority:

1. **Unit tests** for pure functions, hooks, utilities (fast, high confidence)
2. **Component tests** for UI components (render + interaction)
3. **Integration tests** for multi-component flows (forms, wizards, data fetching)
4. **E2E tests** only for critical user paths (signup, checkout, auth)

> Rule: Write the smallest test that catches the bug / validates the feature.

## Unit Tests (Vitest / Jest)

### File Naming & Location

```
src/
  utils/
    format-date.ts
    format-date.test.ts      ← co-located
  hooks/
    use-debounce.ts
    use-debounce.test.ts
```

### Structure: AAA Pattern

```typescript
describe("formatDate", () => {
  it("should format ISO string to locale date", () => {
    // Arrange
    const input = "2026-03-04T00:00:00Z";

    // Act
    const result = formatDate(input, "en-US");

    // Assert
    expect(result).toBe("March 4, 2026");
  });

  it('should return "Invalid date" for malformed input', () => {
    expect(formatDate("not-a-date", "en-US")).toBe("Invalid date");
  });
});
```

### Rules

- One assertion per test (prefer focused tests over multi-assert)
- Test behavior, not implementation (don't test internal state)
- Use descriptive test names: `should [expected behavior] when [condition]`
- Mock external dependencies (API calls, timers, localStorage), never mock the unit under test
- For hooks, use `renderHook` from `@testing-library/react`

## Component Tests (Testing Library)

### Principles

- Test what the user sees and does — not internal component state
- Query by accessibility role first: `getByRole`, `getByLabelText`, `getByText`
- Avoid `getByTestId` unless no semantic alternative exists

### Example: Interactive Component

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

describe('SearchBar', () => {
  it('should filter results as user types', async () => {
    const user = userEvent.setup();
    render(<SearchBar items={['Apple', 'Banana', 'Cherry']} />);

    const input = screen.getByRole('searchbox');
    await user.type(input, 'app');

    expect(screen.getByText('Apple')).toBeInTheDocument();
    expect(screen.queryByText('Banana')).not.toBeInTheDocument();
  });

  it('should show empty state when no results match', async () => {
    const user = userEvent.setup();
    render(<SearchBar items={['Apple', 'Banana']} />);

    await user.type(screen.getByRole('searchbox'), 'xyz');

    expect(screen.getByText(/no results/i)).toBeInTheDocument();
  });
});
```

### Animation Testing

```typescript
it('should respect prefers-reduced-motion', () => {
  // Mock media query
  window.matchMedia = vi.fn().mockImplementation((query) => ({
    matches: query === '(prefers-reduced-motion: reduce)',
    media: query,
    addEventListener: vi.fn(),
    removeEventListener: vi.fn(),
  }));

  render(<AnimatedCard />);

  const card = screen.getByRole('article');
  expect(card).toHaveStyle({ transition: 'none' });
});
```

## E2E Tests (Playwright)

### When to Write E2E

- Critical user journeys (auth, checkout, onboarding)
- Multi-page flows that can't be unit tested
- Visual regression on key pages

### Example

```typescript
import { test, expect } from "@playwright/test";

test.describe("Portfolio Gallery", () => {
  test("should open lightbox on image click and navigate", async ({ page }) => {
    await page.goto("/portfolio");

    // Click first image
    await page.getByRole("img", { name: /project alpha/i }).click();

    // Lightbox should be visible
    const lightbox = page.getByRole("dialog");
    await expect(lightbox).toBeVisible();

    // Navigate to next image
    await page.getByRole("button", { name: /next/i }).click();
    await expect(
      page.getByRole("img", { name: /project beta/i }),
    ).toBeVisible();

    // Close with Escape
    await page.keyboard.press("Escape");
    await expect(lightbox).not.toBeVisible();
  });
});
```

### Visual Regression

```typescript
test("hero section matches snapshot", async ({ page }) => {
  await page.goto("/");
  // Wait for animations to complete
  await page.waitForTimeout(1000);
  await expect(page.locator(".hero")).toHaveScreenshot("hero.png", {
    maxDiffPixelRatio: 0.01,
  });
});
```

## Accessibility Testing

Always include a11y checks in component tests:

```typescript
import { axe, toHaveNoViolations } from 'jest-axe';

expect.extend(toHaveNoViolations);

it('should have no accessibility violations', async () => {
  const { container } = render(<NavigationMenu />);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

## Test Coverage Targets

| Type              | Target | Notes                                  |
| ----------------- | ------ | -------------------------------------- |
| Utility functions | 95%+   | Pure functions are easy to cover       |
| Hooks             | 90%+   | Test all branches and edge cases       |
| Components        | 80%+   | Focus on user interactions             |
| Pages             | 60%+   | Critical paths only                    |
| Overall           | 80%+   | Don't chase 100% — diminishing returns |

## TDD Workflow

When the user asks for TDD:

1. **Red:** Write a failing test that describes the desired behavior
2. **Green:** Write the minimum code to make the test pass
3. **Refactor:** Clean up while keeping tests green
4. Show each step explicitly with the test output

## Rules

- Never use `any` in test types — if the real type is complex, create a test helper/factory
- Use `vi.useFakeTimers()` / `vi.useRealTimers()` for time-dependent tests
- Clean up after each test: `afterEach(() => { vi.restoreAllMocks(); })`
- For data fetching, use MSW (Mock Service Worker) over manual fetch mocks
- Group related tests in `describe` blocks but keep nesting ≤ 2 levels deep
