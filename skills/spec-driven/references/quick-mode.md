# Quick Mode

**Goal:** Create a minimal specification for small, ad-hoc tasks without full pipeline ceremony.

> ⛔ **NEVER implement, run, or commit.** Quick mode only produces a TASK.md spec file.

**Trigger:** "Quick fix", "Quick task", "Small change", "Bug fix", "Just do X"

## When to Use

| Use quick mode             | Use full pipeline                   |
| -------------------------- | ----------------------------------- |
| Bug fixes with known cause | New features with multiple stories  |
| Config changes             | Architectural changes               |
| Small UI tweaks            | Features requiring design decisions |
| Adding a field/column      | Multi-component features            |
| One-off scripts            | Anything with unclear scope         |
| Dependency updates         | Features requiring user stories     |

**Rule of thumb:** If you can describe it in one sentence AND it touches ≤3 files, it's a quick task.

## Process

### 1. Clarify the Task

Even in quick mode, the agent MUST verify understanding before writing anything. Quick does not mean "skip alignment" — it means "lighter process, same certainty."

User provides a clear, one-sentence description. If vague, ask for specifics:

- "Fix the login" → Ask: "What's broken? What should happen instead?"
- "Fix: login button returns 401 because token refresh skips expired check" → Clear enough.

**Minimum verification:** Before writing TASK.md, the agent MUST confirm it can answer: *What exactly is the problem? What does "fixed" look like?* If not, ask.

### 2. Create TASK.md

Create the spec file at `.specs/quick/NNN-slug/TASK.md`.

If the task turns out bigger than expected (>3 files, unclear dependencies, design decisions needed), recommend the full pipeline instead.

### 3. Track

Update `.specs/project/STATE.md` with quick task record (see state-management.md Quick Tasks section).

---

## Structure

Quick tasks live separately from planned features:

```
.specs/
└── quick/
    └── NNN-slug/
        └── TASK.md       # Description + verification criteria
```

**TASK.md template:**

```markdown
# Quick Task NNN: [Title]

**Date:** [date]
**Status:** Specification Complete

## Description

[One sentence: what and why]

## Files to Change

- `src/path/to/file.ts` — [what needs to change]
- `src/path/to/other.ts` — [what needs to change]

## Approach

[One sentence describing how to fix it]

## Verification Criteria

- [ ] [How to verify it works]
- [ ] [Expected behavior after fix]
```

---

## Guardrails

- **Max 3 files** — If more, use full pipeline
- **No design decisions** — If you're choosing between approaches, use full pipeline
- **No new dependencies** — Adding packages needs full pipeline review
- **Spec only** — Output is always TASK.md, never code

---

## Tips

- **Quick ≠ sloppy** — Clear spec prevents implementation mistakes
- **When in doubt, go full** — Better to over-specify than under-specify
- **Quick tasks compound** — If you're doing 5+ quick tasks for the same area, it's a feature that needs planning
