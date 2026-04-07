---
name: code-context
description: Review all code in the project and persist a compact analysis in CONTEXT.md covering structure, patterns, flow, performance, security, and dependencies. Also exhaustively maps ALL functionalities into a hierarchical tree (main → sub → sub-sub) written to FUNCTIONALITY_MAP.md. Use whenever the user wants a code review, wants to understand what a codebase does, list all features, map capabilities, generate a functionality inventory, or needs a full breakdown of what the code does — even if they just say "what does this code do?" or "review this project".
---

# Code Context

Analyze the current project and produce three output files inside a `context/` folder in the project root: `CONTEXT.md`, `FUNCTIONALITY_MAP.md`, and `ARCHITECTURE.drawio`. Create the folder if it does not exist.

## Steps

### 1. Explore the codebase
Read all source and test files. Map the directory structure, entry points, module boundaries, and data flow. Test files are authoritative documentation of expected behavior — read them.

### 2. Write CONTEXT.md
Use bullet points and short sentences. Flag bugs, risks, or improvement opportunities with `⚠️`.

**Structure & Logic**
- High-level architecture and module layout
- Core logic flow and how data moves through the system
- Key classes/functions and their responsibilities
- How modules and components interact

**Patterns & Practices**
- Design patterns in use (e.g., factory, strategy, retry/timeout, topological sort)
- Abstraction quality and code reuse
- Naming conventions and consistency

**Performance & Scalability**
- Bottlenecks or inefficiencies (e.g., N+1 queries, blocking I/O)
- Concurrency and parallelism usage
- Resource management (connections, memory, threads)

**Security & Maintainability**
- Input validation and credential handling
- Coupling and testability of components

**Error Handling & Logging**
- How exceptions are caught, propagated, and surfaced
- Retry logic and fallback behavior
- What is logged and at what level

**Dependencies & Compatibility**
- External libraries and their roles
- Version or compatibility concerns

### 3. Write FUNCTIONALITY_MAP.md
A functionality is any discrete thing the code **does** or **enables**: APIs, CLI commands, UI interactions, background jobs, data transformations, validations, business rules, auth, permissions, configuration, error handling paths, integrations, and internal utilities.

Organize into three levels:
- **Level 1 — Main functionalities**: Top-level capabilities (3–15 items).
- **Level 2 — Sub-functionalities**: Distinct operations or modes within each main functionality.
- **Level 3 — Sub-sub-functionalities**: Concrete behaviors, edge cases, and implementation details using actual code names (function names, route paths, config keys, class names). Add a Level 4 if a Level 3 item branches significantly.

Use this exact format:

```markdown
# Functionality Map: [Project Name]

> [One sentence describing what this project is and does]

---

## 1. [Main Functionality]
[One sentence explaining what this area covers]

### 1.1 [Sub-functionality]
[Optional one-line description]

- **1.1.1** [Sub-sub-functionality]: [what triggers it, what it produces, key constraints]
- **1.1.2** [Sub-sub-functionality]: ...

### 1.2 [Sub-functionality]
...

---

## 2. [Main Functionality]
...
```

Flag unclear or undocumented items with `⚠️ [unclear/incomplete]`.

### 4. Write ARCHITECTURE.drawio
Create `context/ARCHITECTURE.drawio` with **three pages** using the `/draw-io` skill (arrows at back layer, 30px+ internal margin, no background fill, legend where needed, checklist verified before finalizing).

**Page 1 — Process Flow**
Logical execution path from entry point to output. Show how a request or trigger moves through modules, transformations, and decisions. Use rectangles for steps, diamonds for decisions, and labeled directional arrows.

**Page 2 — Code Components**
Structural view of every significant class, function group, and module. Show relationships (inheritance, composition, dependency). Group related items in background frames per module or layer. Use actual code names.

**Page 3 — Data Flow**
What data objects move between components, in which direction, and in what format (e.g., dict, dataclass, list, DB row). Use a distinct arrow color or style to differentiate from structural arrows. Include a legend.

## Output Rules
- Create the `context/` folder if it does not exist, then create or overwrite all three files inside it: `context/CONTEXT.md`, `context/FUNCTIONALITY_MAP.md`, `context/ARCHITECTURE.drawio`.
- Do not describe what you're about to do — just do it and report findings.
