---
name: code-review
description: Review all code in the project and persist a compact analysis in CONTEXT.md covering structure, patterns, flow, performance, security, and dependencies.
---

# Code Review

Perform a comprehensive code review of the current project and write the results to `CONTEXT.md` in the project root.

## Steps

1. **Explore the codebase** — Read all relevant source files. Understand the full directory structure, entry points, and module boundaries.

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

## Output Rules

- Write results to `CONTEXT.md` in the project root (create or overwrite)
- Be **compact and concise** — use bullet points and short sentences
- Organize by the sections above
- Flag anything that is a potential bug, risk, or improvement opportunity with a `⚠️` marker
- Do not describe what you're about to do — just do it and report findings
