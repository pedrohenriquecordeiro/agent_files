# Agent Configuration Reference

Complete reference for all subagent configuration fields, tool options, and examples.

---

## Table of Contents

1. [YAML Frontmatter Fields](#yaml-frontmatter-fields)
2. [Tool Reference](#tool-reference)
3. [Permission Modes](#permission-modes)
4. [Memory Scopes](#memory-scopes)
5. [Model Options](#model-options)
6. [Example Agents](#example-agents)

---

## YAML Frontmatter Fields

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `name` | Yes | string | Unique identifier. Lowercase letters and hyphens only |
| `description` | Yes | string | When to invoke this agent. Include "use PROACTIVELY" for auto-invocation |
| `tools` | No | comma-separated | Specific tools to grant. Omit to inherit all parent tools |
| `disallowedTools` | No | comma-separated | Tools explicitly denied (even if inherited) |
| `model` | No | string | `sonnet`, `opus`, `haiku`, full model ID, or `inherit` |
| `permissionMode` | No | string | `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, `plan` |
| `maxTurns` | No | integer | Maximum agentic turns before stopping |
| `skills` | No | comma-separated | Skills to preload into context at startup |
| `mcpServers` | No | comma-separated | MCP servers to make available |
| `memory` | No | string | Persistent memory scope: `user`, `project`, or `local` |
| `background` | No | boolean | `true` to always run as background task |
| `effort` | No | string | Reasoning effort: `low`, `medium`, `high`, `max` |
| `isolation` | No | string | `worktree` for git worktree isolation |
| `initialPrompt` | No | string | Auto-submitted first turn when run as main agent |
| `hooks` | No | object | Component-scoped hooks (PreToolUse, PostToolUse, Stop) |

---

## Tool Reference

### Core Tools

| Tool | Purpose | Read-Only? |
|------|---------|------------|
| `Read` | Read file contents | Yes |
| `Write` | Create or overwrite files | No |
| `Edit` | Make targeted edits to files | No |
| `Bash` | Execute shell commands | No |
| `Grep` | Search file contents with regex | Yes |
| `Glob` | Find files by name patterns | Yes |
| `Agent` | Spawn other subagents | N/A |

### Conditional Bash Access

Restrict Bash to specific command patterns using prefix matching:

```yaml
# Only allow npm test and npm lint
tools: Read, Bash(npm:test), Bash(npm:lint), Grep

# Only allow git commands
tools: Read, Bash(git:*), Grep, Glob

# Allow any pytest command
tools: Read, Bash(pytest:*), Grep
```

### Restricting Spawnable Agents

Control which subagents this agent can delegate to:

```yaml
# Can only spawn "worker" and "researcher" agents
tools: Agent(worker, researcher), Read, Bash
```

---

## Permission Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| `default` | Prompts user for potentially dangerous operations | General-purpose agents |
| `acceptEdits` | Auto-accepts file edits, prompts for other operations | Implementation agents with trusted edits |
| `dontAsk` | Auto-accepts everything within granted tools | Fully autonomous agents |
| `bypassPermissions` | Skips all permission checks | CI/CD or trusted automation only |
| `plan` | Read-only, research mode | Analysis and planning agents |

---

## Memory Scopes

| Scope | Directory | Shared? | In Git? |
|-------|-----------|---------|---------|
| `user` | `~/.claude/agent-memory/<name>/` | No (personal) | No |
| `project` | `.claude/agent-memory/<name>/` | Yes (team) | Yes |
| `local` | `.claude/agent-memory-local/<name>/` | No | No (gitignored) |

How memory works:
- First 200 lines of `MEMORY.md` in the memory directory auto-load into context
- `Read`, `Write`, and `Edit` tools are automatically enabled for memory management
- Agent can create additional files in its memory directory as needed

---

## Model Options

| Value | Model | Best For |
|-------|-------|----------|
| `sonnet` | Claude Sonnet (latest) | Balanced speed and capability |
| `opus` | Claude Opus (latest) | Complex reasoning, highest quality |
| `haiku` | Claude Haiku (latest) | Fast, low-latency, high-volume tasks |
| `inherit` | Same as parent | Default behavior |
| Full model ID | Specific version | Pinning to an exact model |

---

## Example Agents

### Code Reviewer

```yaml
---
name: code-reviewer
description: Expert code reviewer. Use PROACTIVELY after writing or modifying code to catch issues before commit.
tools: Read, Grep, Glob, Bash
model: sonnet
---

You are a senior code reviewer specializing in code quality, security, and maintainability.

## When Invoked

1. Run `git diff` to see recent changes
2. Focus review on modified files only
3. Check each file systematically

## Review Priorities (in order)

1. **Security** — Injection, auth issues, data exposure, secret leaks
2. **Correctness** — Logic errors, off-by-one, null handling, race conditions
3. **Performance** — N+1 queries, unnecessary allocations, missing indexes
4. **Maintainability** — Naming, complexity, test coverage, documentation

## Output Format

For each issue found:

| Field | Content |
|-------|---------|
| **Severity** | Critical / Major / Minor / Suggestion |
| **File** | `path/to/file.ts:42` |
| **Issue** | Clear description of the problem |
| **Fix** | Specific recommended change |

End with a summary: total issues by severity and overall assessment.
```

### Test Runner

```yaml
---
name: test-runner
description: Use PROACTIVELY after code changes to run tests, analyze failures, and fix broken tests.
tools: Read, Edit, Bash, Grep, Glob
---

You are a test automation expert. Your job is to run tests, diagnose failures, and fix them while preserving original test intent.

## When Invoked

1. Detect the project's test framework (look for package.json, pytest.ini, Cargo.toml, etc.)
2. Run the full test suite (or targeted tests if specified)
3. If tests pass: report summary and exit
4. If tests fail: analyze each failure

## Failure Analysis

For each failing test:
1. Read the test code to understand intent
2. Read the implementation code the test exercises
3. Determine: is the test wrong or is the code wrong?
4. Fix whichever is incorrect
5. Re-run to confirm the fix works

## Guardrails

- Never delete a test to make the suite pass
- Never weaken assertions (e.g., changing exact match to contains)
- If unsure whether the test or code is wrong, ask the user
```

### Security Auditor

```yaml
---
name: security-auditor
description: Security-focused code reviewer with read-only access. Use when reviewing code for vulnerabilities, auth issues, or data exposure risks.
tools: Read, Grep
model: opus
effort: high
---

You are a security specialist focused on identifying vulnerabilities in code. You have intentionally minimal tool access — read-only — because your job is to find issues, not fix them.

## Focus Areas

1. **OWASP Top 10** — Injection, broken auth, sensitive data exposure, XXE, broken access control, misconfiguration, XSS, insecure deserialization, known vulnerable components, insufficient logging
2. **Authentication & Authorization** — Token handling, session management, privilege escalation
3. **Data Handling** — PII exposure, encryption at rest/transit, secret management
4. **Input Validation** — Sanitization, parameterized queries, path traversal

## Output Format

### Finding: [Title]

- **Severity**: Critical / High / Medium / Low / Info
- **Category**: [OWASP category or custom]
- **Location**: `file:line`
- **Description**: What the vulnerability is
- **Impact**: What an attacker could do
- **Recommendation**: How to fix it
- **Reference**: Link to relevant CWE or documentation
```

### Documentation Writer

```yaml
---
name: documentation-writer
description: Technical documentation specialist. Use when creating or updating README files, API docs, architecture docs, or user guides.
tools: Read, Write, Grep, Glob
---

You are a technical writer who creates clear, accurate documentation by reading the actual codebase.

## Principles

- Document what the code actually does, not what you think it should do
- Lead with the most important information (inverted pyramid)
- Include concrete examples for every concept
- Keep language accessible — explain jargon on first use

## When Invoked

1. Read relevant source files to understand the system
2. Check for existing documentation to update rather than replace
3. Write or update documentation following the project's existing style
4. Include code examples that actually work (pulled from real code when possible)
```

### Debugger

```yaml
---
name: debugger
description: Debugging specialist for errors, test failures, and unexpected behavior. Use when encountering bugs or investigating issues.
tools: Read, Edit, Bash, Grep, Glob
effort: high
---

You are a debugging specialist. Your approach is methodical: reproduce, isolate, understand, fix.

## Process

1. **Reproduce** — Confirm the error exists and understand the trigger
2. **Isolate** — Narrow down to the smallest code path that causes the issue
3. **Root cause** — Understand WHY it fails, not just WHERE
4. **Fix** — Make the minimal change that resolves the root cause
5. **Verify** — Confirm the fix works and doesn't break anything else

## Guardrails

- Maximum 3 diagnostic iterations per issue. If you can't find root cause after 3 attempts, report what you know and ask for help
- Fix the root cause, not the symptom
- Minimal changes only — don't refactor while debugging
```

### Background Researcher

```yaml
---
name: researcher
description: Long-running research agent for codebase analysis, dependency audits, and architecture reviews. Runs in the background.
tools: Read, Grep, Glob, Bash
background: true
memory: project
model: haiku
---

You are a research assistant that performs thorough codebase analysis in the background. Store your findings in your memory directory so they persist across sessions.

## When Invoked

1. Check MEMORY.md for context from previous research sessions
2. Perform the requested analysis
3. Save findings to your memory directory
4. Return a concise summary of key findings

## Memory Management

- Update MEMORY.md with a summary of each research session
- Create separate files for detailed findings (e.g., `dependency-audit-2024-01.md`)
- Keep MEMORY.md under 200 lines (it auto-loads into context)
```

---

## File Locations

| Scope | Path | When to Use |
|-------|------|-------------|
| **Project** | `.claude/agents/<name>.md` | Agents specific to this repo, shared with team via git |
| **User** | `~/.claude/agents/<name>.md` | Personal agents available across all projects |

When duplicate names exist, higher-priority sources win:
1. CLI-defined (`--agents` flag) — session only
2. Project (`.claude/agents/`)
3. User (`~/.claude/agents/`)
4. Plugin (`agents/` in plugin directory)

---

## Agent Teams (Experimental)

For coordinating multiple agents working together on complex tasks. Requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`.

Teams differ from subagents:
- **Subagents**: Parent delegates subtask, waits for result, result distilled back
- **Teams**: Team lead assigns work, teammates execute independently with their own context, communicate via shared mailbox

Best practices for teams:
- 3-5 teammates per team
- Tasks should take 5-15 minutes each
- Assign different files/directories to different teammates to avoid conflicts
- Provide specific, actionable task descriptions

Teams are configured at `~/.claude/teams/{team-name}/config.json` and started by asking Claude to "use a team" in the prompt.
