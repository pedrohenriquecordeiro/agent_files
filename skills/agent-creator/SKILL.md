---
name: agent-creator
description: >
  Create Claude Code subagents — specialized AI assistants with custom system prompts, tool access,
  and configurations. Use this skill whenever the user wants to create a new subagent, set up
  a specialized agent, build a code reviewer agent, make a test runner agent, configure an agent
  with specific tools, or any request involving creating .md files for the .claude/agents/ directory.
  Also trigger when users say "create an agent", "make me an agent for", "I need a subagent that",
  "set up an agent", "build an agent", or reference the /agents command for creation purposes.
  Do NOT trigger for general questions about how agents work (use claude-code-guide for that).
disable-model-invocation: true
---

# Agent Creator

You create Claude Code subagents — specialized AI assistants that run in their own context window with custom instructions and tool access. Each subagent is a `.md` file with YAML frontmatter for configuration and a markdown body for the system prompt.

Subagents are powerful because they isolate complex tasks into fresh context windows, preventing the main conversation from getting cluttered. A well-designed subagent has a clear purpose, the right tools for the job, and a system prompt that sets it up for success.

---

## Process

### 1. Understand What the User Needs

Figure out what the subagent should do. Key questions to answer (from the conversation or by asking):

- **Purpose** — What specific task or domain does this agent handle?
- **Scope** — Should it be available in just this project or across all projects?
- **Autonomy** — Should it modify code, or only read/analyze?
- **Triggers** — Should Claude use it automatically, or only when explicitly asked?

If the user gives a clear description like "I need a code reviewer agent", go straight to building it. If they're vague, ask one focused question to disambiguate.

### 2. Choose Configuration

Based on the user's needs, select the right configuration options. See [references/configuration.md](references/configuration.md) for the complete field reference.

**Essential decisions:**

| Decision | Options | Default |
|----------|---------|---------|
| **Scope** | Project (`.claude/agents/`) or User (`~/.claude/agents/`) | Project |
| **Tools** | Specific list or inherit all | Inherit all |
| **Model** | `sonnet`, `opus`, `haiku`, or inherit | Inherit |
| **Proactive?** | Include "use PROACTIVELY" in description | No (explicit only) |

**Advanced options** (only when relevant):

- `memory` — For agents that need to remember things across sessions
- `background` — For long-running tasks that shouldn't block the conversation
- `isolation: worktree` — For agents that make code changes you want to review before merging
- `maxTurns` — To cap how long the agent runs
- `skills` — To preload specific skills into the agent's context
- `hooks` — For running shell commands on agent events
- `effort` — To control reasoning depth (`low`, `medium`, `high`, `max`)

### 3. Design the Tool Access

Tool selection is the most important security decision. Follow the principle of least privilege — give the agent only what it needs.

**Common tool profiles:**

| Agent Type | Recommended Tools | Rationale |
|-----------|------------------|-----------|
| **Read-only analyzer** | `Read, Grep, Glob` | Can explore code but not change anything |
| **Code reviewer** | `Read, Grep, Glob, Bash` | Needs Bash for running linters/tests |
| **Implementer** | `Read, Write, Edit, Bash, Grep, Glob` | Full code modification capability |
| **Test runner** | `Read, Edit, Bash, Grep, Glob` | Run tests and fix failures |
| **Documentation writer** | `Read, Write, Grep` | Write docs, read code for context |
| **Security auditor** | `Read, Grep` | Minimal access, read-only analysis |

**Conditional tool access** — Restrict Bash to specific command patterns:
```yaml
tools: Read, Bash(npm:test), Bash(npm:lint), Grep, Glob
```

**Restrict spawnable subagents** — Control which agents this agent can delegate to:
```yaml
tools: Agent(worker, researcher), Read, Bash
```

### 4. Write the System Prompt

The system prompt is the markdown body after the frontmatter. This is where you define the agent's personality, expertise, workflow, and output format.

**Structure a good system prompt:**

1. **Role statement** (1-2 sentences) — Who is this agent and what's their expertise?
2. **Workflow** — Step-by-step process the agent should follow when invoked
3. **Priorities** — Ordered list of what matters most (e.g., security > performance > style)
4. **Output format** — How results should be structured
5. **Guardrails** — What the agent should avoid doing

**Writing tips:**

- Be specific about the domain: "You are a security reviewer specializing in OWASP Top 10 vulnerabilities" beats "You are a code reviewer"
- Include concrete action steps: "When invoked: 1) Run git diff to see recent changes 2) Focus on modified files 3) Begin review"
- Define output format explicitly so results are consistent and useful
- Explain *why* things matter rather than just listing rules — the model reasons better with context
- Keep it focused — one agent, one job. If you need two jobs, make two agents

### 5. Generate the Agent File

Combine frontmatter and system prompt into a single `.md` file:

```markdown
---
name: agent-name
description: What this agent does and when to use it
tools: Tool1, Tool2, Tool3
---

Your system prompt goes here.

## When Invoked

1. First step
2. Second step
3. Third step

## Output Format

Structure your findings as:
...
```

### 6. Save and Verify

Save the file to the appropriate location:

- **Project scope**: `.claude/agents/<agent-name>.md`
- **User scope**: `~/.claude/agents/<agent-name>.md`

Create the directory if it doesn't exist. Use kebab-case for the filename (matching the `name` field).

After saving, confirm the agent is available by listing the directory contents.

---

## Naming Conventions

- **name field**: lowercase letters and hyphens only (e.g., `code-reviewer`, `test-runner`)
- **filename**: must match the name field with `.md` extension
- **description**: start with what it does, then when to use it. Include "use PROACTIVELY" if it should auto-trigger

---

## Common Patterns

### Proactive Agent
Automatically invoked by Claude when relevant:
```yaml
description: Expert code reviewer. Use PROACTIVELY after writing or modifying code.
```

### Read-Only Agent
Safe for analysis without side effects:
```yaml
tools: Read, Grep, Glob
```

### Agent with Memory
Remembers context across sessions:
```yaml
memory: project  # or "user" for cross-project, "local" for gitignored
```
The agent gets a persistent directory where it can store notes in MEMORY.md. First 200 lines of MEMORY.md auto-load into context.

### Background Agent
Runs without blocking the conversation:
```yaml
background: true
```
Background agents auto-deny any permission prompts, so make sure their tools are pre-approved or set `permissionMode: dontAsk`.

### Isolated Agent
Gets its own git worktree for safe code changes:
```yaml
isolation: worktree
```
Changes stay on a separate branch until the main agent reviews and merges.

### Agent with Skills
Preloads specific skills into context:
```yaml
skills: tlc-spec-driven, simplify
```

### Agent with Hooks
Runs shell commands on lifecycle events:
```yaml
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate-command.sh"
```

---

## What NOT to Do

- **Don't create overlapping agents** with the same role — it confuses delegation
- **Don't give unnecessary tool access** — a reviewer doesn't need Write
- **Don't use agents for simple tasks** — single-step operations don't benefit from a separate context
- **Don't mix concerns** — one agent, one clear job
- **Don't forget the description** — it's the primary mechanism Claude uses to decide when to invoke the agent

---

## Reference

For the complete configuration field reference, tool list, and example agents, read [references/configuration.md](references/configuration.md).
