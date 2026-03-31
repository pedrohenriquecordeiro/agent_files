---
name: code-context
description: Review all code in the project and persist a compact analysis in CONTEXT.md covering structure, patterns, flow, performance, security, and dependencies. Also exhaustively maps ALL functionalities into a hierarchical tree (main → sub → sub-sub) written to FUNCTIONALITY_MAP.md. Use whenever the user wants a code review, wants to understand what a codebase does, list all features, map capabilities, generate a functionality inventory, or needs a full breakdown of what the code does — even if they just say "what does this code do?" or "review this project".
---

# Code Review

Perform a comprehensive code review of the current project and write the results to `CONTEXT.md` in the project root. Also produce a complete functionality map written to `FUNCTIONALITY_MAP.md`.

## Steps

1. **Explore the codebase** — Read all relevant source files. Understand the full directory structure, entry points, module boundaries, and how data flows through the system. Also read test files — they document expected behaviors precisely.

2. **Analyze and document the following in CONTEXT.md:**

### Structure & Logic
- High-level architecture and module layout
- Core logic flow and how data moves through the system
- Key functions/classes and their responsibilities
- How modules and components interact

### Patterns & Practices
- Design patterns used (e.g., retry/timeout, topological sort, factory, strategy)
- Code reuse and abstraction quality
- Naming conventions and consistency

### Performance & Scalability
- Bottlenecks or inefficiencies (e.g., N+1 queries, blocking I/O)
- Concurrency and parallelism usage
- Resource management (connections, memory, threads)

### Security & Maintainability
- Input validation and credential handling
- Error propagation and failure modes
- Coupling and testability of components

### Error Handling & Logging
- How exceptions are caught and surfaced
- Retry logic and fallback behavior
- What is logged and at what level

### Dependencies & Compatibility
- External libraries and their roles
- Any version or compatibility concerns

3. **Build and write the functionality map to FUNCTIONALITY_MAP.md:**

A functionality is any discrete thing the code **does** or **enables**: exposed APIs, CLI commands, UI interactions, background jobs, data transformations, validations, business rules, auth, permissions, configuration, error handling paths, integrations, and internal utilities that compose higher-level behaviors.

Organize into three levels:

- **Level 1 — Main functionalities**: Top-level capabilities (3–15 items). Think: "this system does X, Y, Z."
- **Level 2 — Sub-functionalities**: Distinct operations or modes within each main functionality.
- **Level 3 — Sub-sub-functionalities**: Concrete behaviors, edge cases, options, and implementation details. Use actual names from the code (function names, route paths, config keys, class names). If a level-3 item branches significantly, add a level 4 rather than flattening.

Use this exact format for FUNCTIONALITY_MAP.md:

```markdown
# Functionality Map: [Project Name]

> [One sentence describing what this project is and does]

---

## 1. [Main Functionality]
[One sentence explaining what this area covers]

### 1.1 [Sub-functionality]
[Optional one-line description]

- **1.1.1** [Sub-sub-functionality]: [Brief explanation — what triggers it, what it produces, any key constraints]
- **1.1.2** [Sub-sub-functionality]: ...

### 1.2 [Sub-functionality]
...

---

## 2. [Main Functionality]
...
```

## Output Rules

- Write `CONTEXT.md` and `FUNCTIONALITY_MAP.md` to the project root (create or overwrite each)
- **CONTEXT.md**: be compact and concise — use bullet points and short sentences; flag bugs, risks, or improvement opportunities with `⚠️`
- **FUNCTIONALITY_MAP.md**: be exhaustive — include every behavior; use actual code names; one entry per behavior; flag unclear/undocumented items with `⚠️ [unclear/incomplete]`
- Do not describe what you're about to do — just do it and report findings
