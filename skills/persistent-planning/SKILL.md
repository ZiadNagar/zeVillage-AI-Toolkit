---
name: persistent-planning
description: "Use this skill when working on multi-step tasks, complex projects, or any work that spans multiple interactions. Triggers for 'plan this project', 'break this down', 'track progress', 'continue where we left off', 'what is the status', or any task requiring more than 3 steps to complete. Provides a filesystem-based planning system where the AI agent writes plans, tracks progress, and maintains context across sessions using structured markdown files. Essential for long-running tasks like migrations, refactors, feature builds, and multi-file changes."
license: Complete terms in LICENSE.txt
---

# Persistent Planning

You are managing a complex task that requires structured planning and progress tracking. Use the filesystem as persistent memory — write plans, track progress, and maintain context so work survives across sessions.

## Core Principle

**The filesystem is your memory.** Write everything down. If it's not in a file, it doesn't exist between sessions.

## File Structure

Create a `.plan/` directory in the project root (or use an existing planning directory):

```
.plan/
├── plan.md          # The master plan — goals, phases, current status
├── progress.md      # Running log of completed work with timestamps
└── context.md       # Key decisions, constraints, and discoveries
```

## 1. plan.md — The Master Plan

Create this file at the start of any multi-step task.

```markdown
# Plan: [Task Title]

## Goal
[One sentence: what does "done" look like?]

## Constraints
- [Timeline, tech stack, dependencies, blockers]

## Phases

### Phase 1: [Name]
- [ ] Step 1.1 — [specific, actionable task]
- [ ] Step 1.2 — [specific, actionable task]
- [x] Step 1.3 — [completed task]

### Phase 2: [Name]
- [ ] Step 2.1 — [specific, actionable task]

## Current Focus
> Phase 1, Step 1.2 — [brief description of what's actively being worked on]

## Open Questions
- [Anything that needs user input or research]
```

### Rules for plan.md

- Every step must be **specific and actionable** — "refactor auth" is bad, "extract login form into `LoginForm` component" is good
- Update `Current Focus` every time you start a new step
- Check off steps (`[x]`) immediately when completed
- Keep phases to 3-7 steps each — split larger phases
- Never delete completed steps — they serve as documentation

## 2. progress.md — The Work Log

Append to this file after completing each significant step.

```markdown
# Progress Log

## [Date or Session Identifier]

### Completed
- ✅ Extracted `LoginForm` component from `pages/auth.tsx`
  - Created `components/LoginForm.tsx` (47 lines)
  - Updated imports in `pages/auth.tsx`
  - Added unit test `LoginForm.test.tsx`

### Files Changed
- `components/LoginForm.tsx` — CREATED
- `pages/auth.tsx` — modified (removed inline form)
- `components/LoginForm.test.tsx` — CREATED

### Decisions Made
- Used controlled form pattern instead of `useRef` for inputs
  - Reason: consistent with existing form patterns in the codebase

### Blockers / Notes
- Need to confirm email validation regex with the user
```

### Rules for progress.md

- Always list **files changed** with what happened (CREATED, modified, deleted)
- Record **decisions made** with reasoning — future you needs this
- Append new entries at the top (newest first)
- Keep entries concise but specific enough to resume from

## 3. context.md — Persistent Context

Store discoveries, constraints, and decisions that affect the entire task.

```markdown
# Context

## Project Facts
- Framework: Next.js 14 with App Router
- Database: PostgreSQL via Drizzle ORM
- Auth: NextAuth.js v5
- Deployment: Vercel

## Key Decisions
| Decision | Rationale | Date |
| -------- | --------- | ---- |
| Use server actions over API routes | Reduces boilerplate, team prefers colocation | 2025-01-15 |

## Patterns Discovered
- All forms use controlled inputs with `useActionState`
- Error handling follows `Result<T, E>` pattern in `lib/result.ts`
- CSS uses `--ui-*` custom properties from `globals.css`

## Constraints
- Must maintain backward compatibility with v2 API
- Cannot modify `legacy/` directory — shared with another team
```

### Rules for context.md

- Update when you discover project patterns or constraints
- Record decisions that affect future steps
- Keep it factual — no opinions, just observations

## Workflow

### Starting a New Task

1. **Create `.plan/` directory** if it doesn't exist
2. **Write `plan.md`** — break the task into phases and steps
3. **Write `context.md`** — record any known constraints and project facts
4. **Create empty `progress.md`** with the header
5. **Update `Current Focus`** in plan.md to the first step
6. **Begin work** on the first step

### Resuming Work (Continuing a Previous Session)

1. **Read all three files** — plan.md, progress.md, context.md
2. **Check `Current Focus`** in plan.md — this is where you left off
3. **Review recent progress.md entries** — understand what was just completed
4. **Continue from where you left off** — don't re-plan, don't restart

### During Work

1. **Before starting a step:** Update `Current Focus` in plan.md
2. **After completing a step:** Check it off in plan.md, append to progress.md
3. **When you learn something new:** Add it to context.md
4. **When blocked:** Add to `Open Questions` in plan.md and note in progress.md

### Completing a Task

1. **Verify all steps are checked off** in plan.md
2. **Write a final progress.md entry** summarizing what was accomplished
3. **Update plan.md** — change `Current Focus` to "✅ Complete"

## Step Granularity Guide

| Task Size | Steps Per Phase | Example |
| --------- | --------------- | ------- |
| Small (< 1 hour) | 2-3 steps total | Fix a bug, add a field |
| Medium (1-4 hours) | 3-5 steps per phase, 2-3 phases | Add a feature, refactor a module |
| Large (multi-day) | 5-7 steps per phase, 3-5 phases | Migration, new subsystem |

## Anti-Patterns

| Don't | Do Instead |
| ----- | ---------- |
| Plan everything upfront in extreme detail | Plan Phase 1 in detail, outline future phases |
| Skip progress logging for "small" changes | Log everything — small changes compound |
| Rewrite plan.md from scratch each session | Append and update — history is valuable |
| Store context in your "memory" | Write it to context.md — files persist, memory doesn't |
| Create plans with vague steps | Every step should pass the "could I start this right now?" test |
| Delete completed steps from plan.md | Check them off — they're your receipt |

## Integration with Other Skills

When using persistent-planning alongside other skills:

- **`project-scaffold`** — After planning, use project-scaffold to generate the structure
- **`git-workflow`** — Create a branch per phase, commit after each step
- **`code-review`** — Review completed phases before starting the next
- **`testing`** — Include test steps in every phase, not just at the end
- **`refactoring`** — Use the plan to track refactoring progress across files
