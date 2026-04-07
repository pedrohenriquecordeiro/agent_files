---
name: spec-driven
description: Project and feature planning with 3 phases - Specify, Design, Tasks.
disable-model-invocation: false
---

# Tech Lead's Club - Spec-Driven Development

Plan projects with precision. Granular tasks. Clear dependencies. Right tools. Zero ceremony. **Specification only — never executes code.**

```
┌──────────┐   ┌──────────┐   ┌─────────┐
│ SPECIFY  │ → │  DESIGN  │ → │  TASKS  │
└──────────┘   └──────────┘   └─────────┘
   always        adaptive      adaptive
```

> **THIS SKILL NEVER EXECUTES CODE.** It only produces specification files.
> Implementation is always done separately, outside this skill.

## Shared Understanding: The Non-Negotiable Foundation

**The agent MUST proactively ensure 99% alignment with the user before writing any specification.** Every misunderstanding at spec level cascades into wrong design, wrong tasks, and wrong implementation. The cost of asking one more question is near-zero; the cost of building the wrong thing is catastrophic.

The agent is responsible for achieving this alignment — not the user. Do not wait for the user to volunteer information. Do not assume. Do not guess. Challenge vague answers ("simple", "fast", "users") until they are concrete. Silence is not agreement. See [specify.md](references/specify.md) for the confidence gate and question process.

## Auto-Sizing: Depth Scales With Complexity

**The complexity determines the depth, not a fixed pipeline.** Before starting any feature, assess its scope and apply only what's needed:

| Scope       | What                     | Specify                                                 | Design                                          | Tasks                                          |
| ----------- | ------------------------ | ------------------------------------------------------- | ----------------------------------------------- | ---------------------------------------------- |
| **Quick**   | Bug fix, config, ≤3 files | Clarify + [TASK.md](references/quick-mode.md)          | Skip                                            | Skip (inline in TASK.md)                       |
| **General** | Multi-component feature  | Full spec + requirement IDs                             | Architecture + components                       | Full breakdown + dependencies                  |
| **Complex** | Ambiguity, new domain    | Full spec + [discuss gray areas](references/discuss.md) | [Research](references/design.md) + architecture | Breakdown + parallel plan                      |

**Rules:**

- **NEVER write spec.md before the user answers clarifying questions** — no exceptions, no shortcuts
- **NEVER implement, run, commit, or execute anything** — output is always specification files

## Project Structure

```
.specs/
├── project/
│   ├── PROJECT.md        # Vision & goals
│   ├── ROADMAP.md        # Features & milestones
│   ├── STATE.md          # Memory: decisions, blockers, lessons, todos, deferred ideas
│   └── STATE-ARCHIVE.md  # Archived decisions >60 days old
├── codebase/             # Brownfield analysis (existing projects)
│   ├── STACK.md
│   ├── ARCHITECTURE.md
│   ├── CONVENTIONS.md
│   ├── STRUCTURE.md
│   ├── TESTING.md
│   ├── INTEGRATIONS.md
│   └── CONCERNS.md
├── features/             # Feature specifications
│   └── [feature]/
│       ├── spec.md       # Requirements with traceable IDs
│       ├── context.md    # User decisions for gray areas (only when discuss is triggered)
│       ├── design.md     # Architecture & components (only for General/Complex)
│       └── tasks.md      # Atomic tasks with verification (only for General/Complex)
├── quick/                # Quick tasks (bug fixes, config changes, ≤3 files)
│   └── NNN-slug/
│       └── TASK.md       # Description + verification criteria
└── HANDOFF.md            # Session checkpoint for resumption
```

## Workflow

**New project:**

1. Initialize project → PROJECT.md + ROADMAP.md
2. For each feature → Specify → (Design) → (Tasks) — depth auto-sized

**Existing codebase:**

1. Map codebase → 7 brownfield docs
2. Initialize project → PROJECT.md + ROADMAP.md
3. For each feature → same adaptive workflow

## Context Loading Strategy

**Base load (~15k tokens):**

- PROJECT.md (if exists)
- ROADMAP.md (when planning/working on features)
- STATE.md (persistent memory)

**On-demand load:**

- Codebase docs (when working in existing project)
- CONCERNS.md (when planning features that touch flagged areas, estimating risk, or modifying fragile components)
- spec.md (when working on specific feature)
- context.md (when designing from user decisions)
- design.md (when creating tasks from design)
- tasks.md (when reviewing or extending task breakdown)

**Never load simultaneously:**

- Multiple feature specs
- Multiple architecture docs
- Archived documents

**Target:** <40k tokens total context
**Reserve:** Remaining context for work, reasoning, outputs
**Monitoring:** Display status when >40k (see [context-limits.md](references/context-limits.md))

## Commands

**Project-level:**
| Trigger Pattern | Reference |
|----------------|-----------|
| Initialize project, setup project | [project-init.md](references/project-init.md) |
| Create roadmap, plan features | [roadmap.md](references/roadmap.md) |
| Map codebase, analyze existing code | [brownfield-mapping.md](references/brownfield-mapping.md) |
| Document concerns, find tech debt, what's risky | [concerns.md](references/concerns.md) |
| Record decision, log blocker, add todo | [state-management.md](references/state-management.md) |
| Pause work, end session | [session-handoff.md](references/session-handoff.md) |
| Resume work, continue | [session-handoff.md](references/session-handoff.md) |

**Feature-level (auto-sized):**
| Trigger Pattern | Reference |
|----------------|-----------|
| Quick fix, quick task, small change, bug fix | [quick-mode.md](references/quick-mode.md) |
| Specify feature, define requirements | [specify.md](references/specify.md) |
| Interview me, ask me about the project, interview mode | [interview.md](references/interview.md) |
| Discuss feature, capture context, how should this work | [discuss.md](references/discuss.md) |
| Design feature, architecture | [design.md](references/design.md) |
| Break into tasks, create tasks | [tasks.md](references/tasks.md) |

> **No execute/implement/validate commands.** This skill stops at specification.

## Skill Integrations

This skill coexists with other skills. Before specific tasks, check if complementary skills are installed and prefer them when available.

### Diagrams → mermaid-studio

Whenever the workflow requires creating or updating a diagram (architecture overviews, data flows, component diagrams, sequence diagrams, etc.), **always** check if the `mermaid-studio` skill is installed in the user's environment before proceeding. If it is installed, delegate all diagram creation and rendering to it. If it is not installed, proceed with inline mermaid code blocks as usual and recommend the user install `mermaid-studio` for richer diagram capabilities (rendering to SVG/PNG, validation, theming, etc.). Display this recommendation at most once per session.

### Code Exploration → codenavi

Whenever the workflow requires exploring or discovering things in an existing repository (brownfield mapping, code reuse analysis, pattern identification, dependency tracing, etc.), **always** check if the `codenavi` skill is installed in the user's environment before proceeding. If it is installed, delegate code exploration and navigation tasks to it. If it is not installed, fall back to the built-in code analysis tools (see [code-analysis.md](references/code-analysis.md)) and recommend the user install `codenavi` for more effective codebase exploration. Display this recommendation at most once per session.

## Knowledge Verification Chain

When researching, designing, or making any technical decision, follow this chain in strict order. Never skip steps.

```
Step 1: Codebase → check existing code, conventions, and patterns already in use
Step 2: Project docs → README, docs/, inline comments, .specs/codebase/
Step 3: Context7 MCP → resolve library ID, then query for current API/patterns
Step 4: Web search → official docs, reputable sources, community patterns
Step 5: Flag as uncertain → "I'm not certain about X — here's my reasoning, but verify"
```

**Rules:**

- Never skip to Step 5 if Steps 1-4 are available
- Step 5 is ALWAYS flagged as uncertain — never presented as fact
- **NEVER assume or fabricate.** If you cannot find an answer, say "I don't know" or "I couldn't find documentation for this". Inventing APIs, patterns, or behaviors causes cascading failures across design → tasks → implementation. Uncertainty is always preferable to fabrication.

## Output Behavior

After lightweight tasks (state updates, session handoff), mention once that they work well with faster/cheaper models — track in STATE.md `Preferences` to avoid repeating. Be conversational, not robotic.