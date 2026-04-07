# Specify

**Goal**: Capture WHAT to build with testable, traceable requirements.

If the feature has ambiguous gray areas (multiple valid approaches for user-facing behavior), the agent will automatically trigger the [discuss gray areas](discuss.md) process within this phase. For clear, well-defined features, it goes straight to the next phase.

## Process

### 1. Clarifying Questions — The Alignment Gate

> **This step is MANDATORY and the agent is PROACTIVELY RESPONSIBLE for it.**
> The agent must ensure 99% shared understanding with the user before writing any spec.
> This is not a formality — it is the most critical step of the entire specification process.

The agent MUST ask structured questions to eliminate ambiguity. The goal is not to check a box — the goal is to reach a state where the agent can confidently say: *"I understand exactly what the user wants, and I would bet on it."*

**Focus areas:**

- **Problem/Goal:** What problem does this solve? Why does it need solving now?
- **Core Functionality:** What are the key actions? What does success look like in the user's head?
- **Scope/Boundaries:** What should it NOT do? Where does this feature end?
- **Success Criteria:** How do we know it's done? What would make the user say "yes, this is exactly what I wanted"?
- **Hidden Assumptions:** What is the agent assuming that the user hasn't explicitly stated?

**How many to ask:** If the prompt is vague, ask at least 5 questions (max 25). If the user has already provided clear context, you may need fewer — but NEVER zero. Even with clear context, the agent MUST ask at least 2-3 confirmation questions to verify its understanding. Calibrate to the actual ambiguity — never ask for information already given, but always verify critical assumptions.

**Format every question with lettered options:**

```
1. What is the primary goal of this feature?
   A. [Option]
   B. [Option]
   C. Other: [please specify]

2. Who is the target user?
   A. [Role/persona]
   B. [Role/persona]
   C. All users
   D. Other: [please specify]

3. What is the scope?
   A. Minimal viable version
   B. Full-featured implementation
   C. Just the backend/API
   D. Just the UI
   E. Other: [please specify]
```

**Wait for answers before proceeding.** Do not draft spec.md until the user has responded to the clarifying questions. This is a hard gate — no exceptions.

**Challenge vagueness in answers — aggressively.** The agent MUST NOT accept vague answers as resolved. Specific patterns to challenge:

| Vague answer | Agent's follow-up |
|---|---|
| "It should be simple" | "Simple for whom? What's the simplest version you'd accept?" |
| "For users" | "Which users specifically? Admins? End consumers? Internal team?" |
| "It should be fast" | "Fast compared to what? What response time would you consider acceptable?" |
| "Standard approach" | "Standard in what context? Can you point to an example you consider standard?" |
| "Just make it work" | "What does 'working' look like? What's the minimum behavior you'd demo?" |
| "You decide" | "I can decide, but let me confirm my assumptions: [state them]. Correct?" |

**Confidence self-check before proceeding:**

After receiving answers, the agent MUST internally assess:

```
┌─────────────────────────────────────────────────────┐
│  CONFIDENCE CHECK                                   │
│                                                     │
│  □ I know WHAT the user wants to build              │
│  □ I know WHY they want it                          │
│  □ I know WHO it's for                              │
│  □ I know what's IN scope and OUT of scope          │
│  □ I know what SUCCESS looks like to the user       │
│  □ I have zero assumptions I haven't verified       │
│                                                     │
│  All checked? → Proceed to write spec               │
│  Any unchecked? → Ask more questions                │
└─────────────────────────────────────────────────────┘
```

**If confidence is below 99%, the agent MUST ask another round of questions.** There is no shortcut. There is no "good enough". The spec must reflect what the user actually wants, not what the agent thinks they want.

### 2. Capture User Stories with Priorities

**P1 = MVP** (must ship), **P2** (should have), **P3** (nice to have)

Each story MUST be **independently specifiable** — it can be specified, reviewed, and later implemented in isolation.

### 3. Write Acceptance Criteria

Use **WHEN/THEN/SHALL** format - it's precise and testable:

- WHEN [event/action] THEN [system] SHALL [response/behavior]

### 4. Confirm with User

User must approve spec (and context.md if discuss was triggered) before proceeding to Design. Do not move forward without explicit confirmation.

---

## Template: `.specs/features/[feature]/spec.md`

```markdown
# [Feature Name] Specification

## Problem Statement

[Describe the problem in 2-3 sentences. What pain point are we solving? Why now?]

## Goals

- [ ] [Primary goal with measurable outcome]
- [ ] [Secondary goal with measurable outcome]

## Out of Scope

Explicitly excluded. Documented to prevent scope creep.

| Feature     | Reason         |
| ----------- | -------------- |
| [Feature X] | [Why excluded] |
| [Feature Y] | [Why excluded] |

---

## User Stories

### P1: [Story Title] ⭐ MVP

**User Story**: As a [role], I want [capability] so that [benefit].

**Why P1**: [Why this is critical for MVP]

**Acceptance Criteria**:

1. WHEN [user action/event] THEN system SHALL [expected behavior]
2. WHEN [user action/event] THEN system SHALL [expected behavior]
3. WHEN [edge case] THEN system SHALL [graceful handling]

**Independent Test**: [How to verify this story works alone - e.g., "Can demo by doing X and seeing Y"]

---

### P2: [Story Title]

**User Story**: As a [role], I want [capability] so that [benefit].

**Why P2**: [Why this isn't MVP but important]

**Acceptance Criteria**:

1. WHEN [event] THEN system SHALL [behavior]
2. WHEN [event] THEN system SHALL [behavior]

**Independent Test**: [How to verify]

---

### P3: [Story Title]

**User Story**: As a [role], I want [capability] so that [benefit].

**Why P3**: [Why this is nice-to-have]

**Acceptance Criteria**:

1. WHEN [event] THEN system SHALL [behavior]

---

## Edge Cases

- WHEN [boundary condition] THEN system SHALL [behavior]
- WHEN [error scenario] THEN system SHALL [graceful handling]
- WHEN [unexpected input] THEN system SHALL [validation response]

---

## Requirement Traceability

Each requirement gets a unique ID for tracking across design, tasks, and validation.

| Requirement ID | Story       | Phase  | Status  |
| -------------- | ----------- | ------ | ------- |
| [FEAT]-01      | P1: [Story] | Design | Pending |
| [FEAT]-02      | P1: [Story] | Design | Pending |
| [FEAT]-03      | P2: [Story] | -      | Pending |

**ID format:** `[CATEGORY]-[NUMBER]` (e.g., `AUTH-01`, `CART-03`, `NOTIF-02`)

**Status values:** Pending → In Design → In Tasks → Ready

**Coverage:** X total, Y mapped to tasks, Z unmapped ⚠️

---

## Success Criteria

How we know the feature is successful:

- [ ] [Measurable outcome - e.g., "User can complete X in < 2 minutes"]
- [ ] [Measurable outcome - e.g., "Zero errors in Y scenario"]
```

---

## Tips

- **The agent owns the questions** — Don't wait for the user to anticipate gaps. Proactively identify what's unclear and ask.
- **P1 = Vertical Slice** — A complete, demo-able feature, not just backend or frontend
- **WHEN/THEN is code** — If you can't write it as a test, rewrite it
- **Requirement IDs are mandatory** — Every story maps to trackable IDs
- **Edge cases matter** — What breaks? What's empty? What's huge?
- **Out of Scope prevents creep** — If it's not here, it doesn't get built
