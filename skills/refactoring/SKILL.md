---
name: refactoring
description: Use this skill when the user asks to refactor code, clean up a file, reduce complexity, extract components, split a large file, modernize legacy code, migrate between frameworks or APIs, detect code smells, or improve code architecture. Also triggers for "this file is too long", "simplify this", "extract this into a hook/component", "migrate from X to Y", or "reduce duplication". Covers React/Vue/Svelte component decomposition, CSS refactoring, animation system cleanup, and design system extraction.
license: Complete terms in LICENSE.txt
---

# Refactoring

You are performing a structured code refactoring. Follow this process to ensure safe, incremental changes.

## Golden Rules

1. **Never change behavior** — refactoring improves structure, not functionality
2. **Small steps** — one refactoring at a time, verify between each
3. **Tests first** — if tests don't exist, write them before refactoring
4. **Explain the why** — every change gets a brief rationale

## Refactoring Process

### Step 1: Assess

Before touching code, analyze:

- What's the current file/function size and complexity?
- What are the code smells? (see catalog below)
- Are there existing tests covering this code?
- What's the risk level? (low: rename, medium: extract, high: restructure)

### Step 2: Plan

Present a numbered plan:

```
Refactoring Plan for ComponentX.tsx (387 lines)

1. Extract form validation logic → useFormValidation hook (low risk)
2. Extract animation variants → motion-variants.ts (low risk)
3. Split into subcomponents: Header, Body, Footer (medium risk)
4. Replace prop drilling with context (medium risk)

Order: 1 → 2 → 3 → 4 (each step is independently useful)
```

### Step 3: Execute

Apply one refactoring at a time. After each, confirm the code still works.

## Code Smell Catalog

### Smells → Fixes

| Smell                | Indicator                         | Fix                                       |
| -------------------- | --------------------------------- | ----------------------------------------- |
| **Long file**        | > 300 lines                       | Extract components/hooks/utils            |
| **God component**    | Does too many things              | Single Responsibility — split             |
| **Prop drilling**    | Props passed through 3+ levels    | Context, composition, or state management |
| **Duplicate logic**  | Same pattern in 3+ places         | Extract to shared hook/util               |
| **Magic numbers**    | Hardcoded `300`, `1.5`, `#3b82f6` | Named constants or design tokens          |
| **Nested ternaries** | `a ? b ? c : d : e`               | Early returns or lookup objects           |
| **useEffect soup**   | Component has 4+ useEffects       | Extract to custom hooks                   |
| **CSS sprawl**       | 500+ line CSS file                | Split by component, use CSS modules       |
| **z-index chaos**    | Random values: 999, 9999          | Token scale: `z-dropdown: 100`            |
| **any abuse**        | TypeScript `any` used > 3 times   | Proper types or generics                  |

## Common Refactoring Patterns

### Extract Custom Hook

**Before:**

```tsx
function SearchPage() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    const controller = new AbortController();
    setLoading(true);
    fetch(`/api/search?q=${query}`, { signal: controller.signal })
      .then((r) => r.json())
      .then(setResults)
      .finally(() => setLoading(false));
    return () => controller.abort();
  }, [query]);

  // ... 200 more lines of JSX
}
```

**After:**

```tsx
function useSearch(query: string) {
  const [results, setResults] = useState<SearchResult[]>([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    const controller = new AbortController();
    setLoading(true);
    fetch(`/api/search?q=${query}`, { signal: controller.signal })
      .then((r) => r.json())
      .then(setResults)
      .finally(() => setLoading(false));
    return () => controller.abort();
  }, [query]);

  return { results, loading };
}

function SearchPage() {
  const [query, setQuery] = useState("");
  const { results, loading } = useSearch(query);
  // Clean focused JSX
}
```

### Extract Component with Composition

**Before:** One component with conditional rendering for 5 states

**After:**

```tsx
// Compound component pattern
<Card>
  <Card.Header title={title} />
  <Card.Body>{children}</Card.Body>
  <Card.Footer actions={actions} />
</Card>
```

### Replace Conditionals with Lookup

**Before:**

```tsx
function getStatusColor(status: string) {
  if (status === "active") return "#22c55e";
  if (status === "pending") return "#f59e0b";
  if (status === "error") return "#ef4444";
  if (status === "disabled") return "#6b7280";
  return "#000000";
}
```

**After:**

```tsx
const STATUS_COLORS = {
  active: "var(--color-success)",
  pending: "var(--color-warning)",
  error: "var(--color-error)",
  disabled: "var(--color-muted)",
} as const satisfies Record<string, string>;

function getStatusColor(status: keyof typeof STATUS_COLORS) {
  return STATUS_COLORS[status] ?? "var(--color-default)";
}
```

### Animation Cleanup

**Before:** Inline animation values scattered across components

**After:**

```tsx
// motion-tokens.ts — single source of truth
export const transitions = {
  spring: { type: "spring", damping: 25, stiffness: 300 },
  snappy: { type: "spring", damping: 30, stiffness: 500 },
  gentle: { type: "spring", damping: 20, stiffness: 150 },
} as const;

export const variants = {
  fadeIn: { initial: { opacity: 0 }, animate: { opacity: 1 } },
  slideUp: { initial: { opacity: 0, y: 20 }, animate: { opacity: 1, y: 0 } },
} as const;
```

## Migration Patterns

When migrating between tools/frameworks:

### Process

1. Create an adapter/compatibility layer
2. Migrate one component at a time
3. Run both old and new in parallel (where possible)
4. Remove the adapter when migration is complete

### Common Migrations

- **CSS → CSS Modules → Tailwind**: Start with utility classes for new code, refactor existing gradually
- **useState → Zustand/Jotai**: Extract state to store, replace useState with useStore, component by component
- **REST → GraphQL**: Keep REST endpoint working, add GraphQL resolver, switch consumers one at a time
- **Class components → Functional**: Rewrite one component at a time, start from leaf components

## Output Format

For each refactoring step, show:

```
### Step N: [Refactoring Name]

**Smell:** [What was wrong]
**Fix:** [What we're doing]
**Risk:** Low / Medium / High

[Code changes with before/after]

**Verify:** [How to confirm this didn't break anything]
```
