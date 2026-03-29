# Review Report Template

Use this template to generate the review report. Save to `.specs/features/[feature]/review.md`.

Adapt sections based on which spec files exist — skip sections that reference files not present for this feature.

---

```markdown
# [Feature Name] — Specification Compliance Review

**Date**: [YYYY-MM-DD]
**Reviewer**: Claude (spec-driven-check-result)
**Spec path**: `.specs/features/[feature]/`

## Spec Files Reviewed

| File | Present | Notes |
|------|---------|-------|
| spec.md | Yes | [version/last modified if available] |
| context.md | Yes/No | [notes] |
| design.md | Yes/No | [notes] |
| tasks.md | Yes/No | [notes] |

---

## Overall Verdict

**[COMPLIANT / PARTIALLY COMPLIANT / NON-COMPLIANT]**

- Requirements passed: [X] / [Total]
- Acceptance criteria passed: [X] / [Total]
- Edge cases covered: [X] / [Total]
- Blockers: [count]
- Major issues: [count]
- Minor issues: [count]

---

## Requirement Compliance

### [REQUIREMENT-ID]: [Brief description]

**Source**: spec.md > [User Story / Section]
**Priority**: [P1/P2/P3]
**Status**: [PASS / FAIL / PARTIAL]

**Acceptance Criteria**:

| # | Criterion | Result | Evidence |
|---|-----------|--------|----------|
| 1 | WHEN [action] THEN system SHALL [behavior] | PASS/FAIL | `file:line` — [what the code does] |
| 2 | WHEN [action] THEN system SHALL [behavior] | PASS/FAIL | `file:line` — [what the code does] |

**Issues** (if any):
- [Severity]: [Description of the gap between spec and code]

---

[Repeat the above block for each requirement ID]

---

## Edge Case Coverage

| Edge Case (from spec.md) | Handled? | Evidence |
|--------------------------|----------|----------|
| WHEN [boundary condition] THEN SHALL [behavior] | YES/NO | `file:line` — [how it's handled] |
| WHEN [error scenario] THEN SHALL [behavior] | YES/NO | `file:line` — [how it's handled] |

---

## Design Compliance

> Skip this section if design.md does not exist.

### Components

| Component | Location (spec) | Location (actual) | Interface Match? | Notes |
|-----------|----------------|-------------------|-----------------|-------|
| [Name] | [specified path] | [actual path] | YES/NO | [deviations] |

### Data Models

| Model | Fields Match? | Relationships Match? | Notes |
|-------|--------------|---------------------|-------|
| [Name] | YES/NO | YES/NO | [deviations] |

### Error Handling

| Scenario (from design.md) | Implemented? | Notes |
|--------------------------|-------------|-------|
| [error scenario] | YES/NO | [how it's handled vs. how it should be] |

### Tech Decisions

| Decision | Followed? | Notes |
|----------|----------|-------|
| [decision from design.md] | YES/NO | [evidence] |

---

## Task Completion

> Skip this section if tasks.md does not exist.

| Task | Status in tasks.md | Verified | "Done When" Criteria Met? | Notes |
|------|-------------------|----------|--------------------------|-------|
| T1: [title] | [Done/Partial/Pending] | YES/NO | [all criteria checked] | [notes] |
| T2: [title] | [Done/Partial/Pending] | YES/NO | [all criteria checked] | [notes] |

---

## Scope Compliance

### Out-of-Scope Check

Items from spec.md "Out of Scope" table — verify none were implemented:

| Out-of-Scope Item | Implemented? | Notes |
|-------------------|-------------|-------|
| [Feature X] | NO (correct) / YES (scope creep) | [evidence] |

### Scope Creep Detection

Code or behavior found that no requirement, acceptance criterion, or design decision calls for:

| Finding | Files | Concern |
|---------|-------|---------|
| [what was found] | `file:line` | [why this may be scope creep] |

> If no scope creep detected, state: "No scope creep detected. Implementation stays within spec boundaries."

---

## Context Decisions

> Skip this section if context.md does not exist.

| Decision (from context.md) | Implemented Correctly? | Evidence |
|---------------------------|----------------------|----------|
| [decision about gray area] | YES/NO | `file:line` — [what the code does] |

---

## Issues Summary

| # | Severity | Requirement | Description | Suggested Fix |
|---|----------|-------------|-------------|---------------|
| 1 | [Blocker/Major/Minor/Cosmetic] | [REQ-ID] | [what's wrong] | [how to fix] |
| 2 | [Blocker/Major/Minor/Cosmetic] | [REQ-ID] | [what's wrong] | [how to fix] |

---

## Requirement Traceability Update

Suggested updates to spec.md traceability table:

| Requirement ID | Current Status | Suggested Status |
|---------------|---------------|-----------------|
| [FEAT]-01 | Implementing | Verified |
| [FEAT]-02 | Implementing | Needs Fix |

---

## Summary

**What's compliant**: [Brief list of what works correctly]

**What needs attention**: [Brief list of issues, ordered by severity]

**Recommended next steps**:
1. [Most critical action]
2. [Next action]
3. [...]
```
