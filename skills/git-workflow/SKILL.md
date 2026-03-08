---
name: git-workflow
description: Use this skill when the user asks about git workflows, commit messages, branching strategies, creating PRs, generating changelogs, writing release notes, squashing commits, resolving merge conflicts, setting up git hooks, or managing a git-based development workflow. Also use when asked to write conventional commits, set up trunk-based development, or create a contribution guide.
license: Complete terms in LICENSE.txt
---

# Git Workflow

You are helping manage a professional git workflow. Follow these standards.

## Commit Message Format

Use [Conventional Commits](https://www.conventionalcommits.org/) with these types:

```
feat:     New feature (correlates with MINOR in semver)
fix:      Bug fix (correlates with PATCH in semver)
docs:     Documentation changes only
style:    Formatting, missing semicolons, whitespace (no code logic change)
refactor: Code change that neither fixes a bug nor adds a feature
perf:     Performance improvement
test:     Adding or updating tests
build:    Build system or external dependencies (webpack, vite, npm)
ci:       CI/CD pipeline changes
chore:    Maintenance tasks, dependency bumps
revert:   Reverts a previous commit
```

### Commit Message Structure

```
<type>(<scope>): <short description>

[optional body — explain WHAT and WHY, not HOW]

[optional footer — BREAKING CHANGE:, Closes #123, Co-authored-by:]
```

### Rules

- Subject line: max 72 characters, imperative mood ("add" not "added")
- Body: wrap at 100 characters per line
- Scope: use the component/module name (e.g., `feat(carousel): add auto-play`)
- Breaking changes: add `!` after type/scope AND a `BREAKING CHANGE:` footer

### Examples

```
feat(animation): add spring-based easing to modal transitions

Replaces CSS ease-in-out with a physics-based spring curve for more
natural motion. Uses framer-motion's spring preset with damping: 25.

Closes #142

---

fix(a11y): restore focus to trigger element when dialog closes

Previously focus was lost to document.body after closing a dialog,
breaking keyboard navigation flow.

---

refactor(hooks)!: rename useAnimationState to useMotion

BREAKING CHANGE: useAnimationState is removed. Replace all imports
with useMotion. The API is identical.
```

## Branching Strategy

### Trunk-Based Development (Recommended for small teams)

```
main ─────────────────────────────────────────►
  ├── feat/carousel-autoplay ──► PR ──► merge
  ├── fix/modal-focus-trap ──► PR ──► merge
  └── chore/deps-update ──► PR ──► merge
```

- `main` is always deployable
- Feature branches live < 2 days
- Use feature flags for long-running work
- Squash-merge PRs for clean history

### GitFlow (For versioned releases)

```
main ──────────────────────────────────────────►
  └── develop ─────────────────────────────────►
       ├── feature/new-dashboard ──► merge to develop
       ├── release/2.1.0 ──► merge to main + tag
       └── hotfix/2.0.1 ──► merge to main + develop
```

## PR Template

When creating a PR description, use this template:

```markdown
## What

[Brief description of what this PR does]

## Why

[Motivation — link to issue/ticket if applicable]

## How

[Key implementation details, architectural decisions]

## Screenshots / Recordings

[For UI changes — before/after screenshots or screen recordings]

## Testing

- [ ] Unit tests added/updated
- [ ] Tested in Chrome, Firefox, Safari
- [ ] Tested on mobile viewport
- [ ] Accessibility tested (keyboard nav, screen reader)

## Checklist

- [ ] No console errors or warnings
- [ ] Bundle size impact checked
- [ ] Documentation updated if needed
- [ ] Breaking changes documented
```

## Changelog Generation

When asked to generate a changelog, follow [Keep a Changelog](https://keepachangelog.com/) format:

```markdown
# Changelog

## [Unreleased]

### Added

- New spring animation system for modal transitions (#142)

### Fixed

- Dialog focus trap now returns focus to trigger element (#156)

### Changed

- Renamed `useAnimationState` hook to `useMotion` (BREAKING)

### Removed

- Deprecated `FadeTransition` component — use `Motion` instead
```

Group entries by: Added, Changed, Deprecated, Removed, Fixed, Security.

## Git Hooks (Recommended Setup)

Suggest these hooks when setting up a project:

```json
// package.json (using husky + lint-staged)
{
  "scripts": {
    "prepare": "husky"
  },
  "lint-staged": {
    "*.{ts,tsx,js,jsx}": ["eslint --fix", "prettier --write"],
    "*.{css,scss}": ["prettier --write"],
    "*.{json,md}": ["prettier --write"]
  }
}
```

- **pre-commit:** lint-staged (format + lint only staged files)
- **commit-msg:** validate conventional commit format
- **pre-push:** run type-check and tests
