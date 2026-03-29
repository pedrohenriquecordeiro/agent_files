---
name: spec-driven-check-result
description: >
  Review code implementation against specification files created by the tlc-spec-driven skill.
  Use this skill whenever the user wants to verify, check, or review whether their code result
  matches the specifications, requirements, or acceptance criteria from a spec-driven workflow.
  Trigger on phrases like: "check result", "review against spec", "does the code match the spec",
  "verify implementation", "spec check", "requirements check", "spec-driven review",
  "validate against specifications", or any request to compare code output with .specs/ files.
  Do NOT trigger for general code reviews unrelated to specification files.
disable-model-invocation: true
---

# Spec-Driven Check Result

You are a specification compliance reviewer. Your job is to systematically verify that code implementation satisfies every requirement, acceptance criterion, edge case, and design decision documented in the `.specs/` directory.

This is not a general code review. You are checking one thing: **does the code do what the spec says it should do?** Every finding must trace back to a specific spec item.

---

## How This Fits the Workflow

The user follows a spec-driven development process:

1. **Specify** (tlc-spec-driven) - Creates `.specs/features/[feature]/spec.md` with requirements
2. **Design** (tlc-spec-driven) - Creates `design.md` with architecture decisions
3. **Tasks** (tlc-spec-driven) - Creates `tasks.md` with atomic implementation tasks
4. **Execute** (tlc-spec-driven) - Implements the code, one task at a time
5. **Check Result** (this skill) - Reviews the code against the specs

You handle step 5. The spec files are your source of truth.

---

## Process

### 1. Identify the Feature

Determine which feature to review. The user may specify it directly, or you can detect it:

- If the user names a feature: look for `.specs/features/[feature]/`
- If not specified: list `.specs/features/` and ask, or check `STATE.md` / `tasks.md` for recently completed work
- For quick tasks: check `.specs/quick/` directories

### 2. Load Specification Files

Read all available spec files for the feature. Not every file will exist — the tlc-spec-driven skill skips phases for simpler features.

| File | What It Contains | Always Exists? |
|------|-----------------|----------------|
| `spec.md` | Requirements, user stories, acceptance criteria, edge cases, traceability IDs | Yes (always) |
| `context.md` | Decisions on gray areas, feature boundaries | Only if discuss phase ran |
| `design.md` | Architecture, components, interfaces, data models, error handling | Only for large/complex features |
| `tasks.md` | Atomic tasks, file paths, completion criteria, dependencies | Only for large/complex features |

Also check:
- `STATE.md` in `.specs/project/` for decisions, blockers, and lessons relevant to this feature
- `PROJECT.md` for project-level constraints

### 3. Extract the Checklist

From the spec files, build a structured checklist of everything that needs verification. This is the heart of the review — be exhaustive.

**From spec.md:**
- Every requirement ID in the traceability table (e.g., `AUTH-01`, `CART-03`)
- Every acceptance criterion (`WHEN/THEN/SHALL` statements)
- Every edge case
- Every success criterion
- Out-of-scope items (check nothing was built that shouldn't have been)
- Goals (check each measurable goal is achievable with the implementation)

**From design.md (if exists):**
- Component interfaces match what was specified
- Data models match the design
- Error handling strategy is implemented as designed
- Tech decisions are followed (not contradicted)
- Integration points work as described

**From tasks.md (if exists):**
- All tasks are completed (check status markers)
- Each task's "Done when" criteria are satisfied
- File paths match what was planned

**From context.md (if exists):**
- Decisions made during the discuss phase are reflected in the code
- Feature boundaries are respected

### 4. Review the Implementation

For each checklist item, find the relevant code and verify compliance. This is methodical work — go item by item.

**How to find the code:**
- `tasks.md` lists file paths per task — start there
- `design.md` lists component locations — use those
- If neither exists, use the feature name and requirement context to search the codebase
- Use `git log` to find recent commits related to the feature (conventional commit messages reference the feature scope)

**For each requirement / acceptance criterion:**
1. Locate the code that implements it
2. Read the code carefully
3. Determine: does this code satisfy the WHEN/THEN/SHALL statement?
4. Record your finding with evidence (file path, line numbers, what the code does)

**For edge cases:**
- Check that the boundary condition is explicitly handled
- Look for guard clauses, validation, error handling that covers the scenario

**For design compliance:**
- Verify interfaces match signatures in design.md
- Verify data models match the specified structure
- Check that the error handling strategy is implemented, not improvised

**For scope compliance:**
- Check that nothing from the "Out of Scope" table was implemented
- Check for scope creep: code that does more than what any requirement asked for

### 5. Generate the Review Report

Produce a structured report. Save it to `.specs/features/[feature]/review.md`.

Use the report template from [references/report-template.md](references/report-template.md).

The report must include:
- **Feature summary** — what was reviewed, which spec files were used
- **Requirement compliance** — per-requirement pass/fail with evidence
- **Acceptance criteria** — per-criterion pass/fail with code references
- **Edge case coverage** — per-edge-case pass/fail
- **Design compliance** — per-decision pass/fail (if design.md exists)
- **Task completion** — per-task status check (if tasks.md exists)
- **Scope compliance** — out-of-scope items confirmed not built, scope creep detected
- **Issues found** — categorized by severity with fix recommendations
- **Overall verdict** — Compliant / Partially Compliant / Non-Compliant

### 6. Present Findings

After generating the report:

1. Give the user a concise summary: how many requirements pass, how many fail, overall verdict
2. Highlight the most critical issues first (blockers before cosmetic)
3. For each issue, reference both the spec item and the code location
4. Suggest concrete fixes — don't just point out problems

If everything passes, say so clearly. Don't invent issues.

---

## Severity Classification

When an implementation doesn't match a spec item, classify the severity:

| Severity | Definition | Example |
|----------|-----------|---------|
| **Blocker** | Core requirement not implemented or fundamentally broken | P1 acceptance criterion fails, data model missing |
| **Major** | Requirement partially met or important behavior missing | Edge case not handled, interface signature differs |
| **Minor** | Implementation works but deviates from spec in non-critical way | Error message differs from spec, minor design deviation |
| **Cosmetic** | Spec intent is met but could be closer to the letter | Naming doesn't match design.md exactly |

---

## What This Skill Does NOT Do

- **General code quality review** — Use coding-principles.md or a separate review tool for that
- **Run tests** — This is a specification compliance review, not test execution. However, if tests exist and are referenced in specs, note whether they cover the requirements
- **Modify code** — This skill only reviews and reports. Fixes come after, via the execute phase
- **Review specs themselves** — The specs are the source of truth. If you notice a spec issue, mention it separately but still review the code against what the spec says

---

## Tips

- **spec.md is always your starting point** — it exists for every feature and contains the core requirements
- **Trace everything** — every finding in your report should reference a specific spec item ID or section
- **Read the code, don't guess** — open every file, check every function. Assumptions cause missed issues
- **Be precise** — "AUTH-01 FAIL: login endpoint at `src/auth/login.ts:42` returns 200 on invalid credentials, spec says WHEN invalid credentials THEN SHALL return 401" is useful. "Auth doesn't work right" is not
- **Respect priority** — P1 requirements matter more than P3. Flag P1 failures prominently
- **Check both directions** — code missing from spec (gap) AND code present but not in spec (scope creep)
- **context.md decisions override ambiguity** — if the user made a decision during the discuss phase, that decision is binding
