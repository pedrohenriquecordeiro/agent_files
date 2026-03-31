# Interview: Project Understanding

**Goal:** Reach mutual understanding about the project through structured, dependency-aware questioning. Each question set resolves one layer of decisions before moving to the next.

**Trigger:** Explicitly via "interview me", "ask me about the project", "interview mode", or when the agent detects high ambiguity across multiple axes simultaneously (unclear goal + unclear scope + unclear user + unclear constraints).

**When to use:** Starting a new project with no existing spec, or when the user's description is too broad to write a spec without guessing foundational decisions first.

**When NOT to use:** If the user has already provided clear context — go straight to [specify.md](specify.md). Don't interview when you can just ask one follow-up question.

---

## How It Works

Analyze what is known and unknown about the project, then generate a dependency-ordered question set. Decisions made earlier unlock or constrain options in later questions.

**Resolution order:**
1. Goal & motivation (why this exists)
2. Target user & context (who it's for)
3. Scope & boundaries (what's in/out)
4. Technical & operational constraints (what limits the solution)
5. Success definition (how we know it worked)
6. Feature-specific decisions (depends on all above)

Present **at least 5 questions per round**. Add more if the project has unresolved dependencies that would force guessing later.

---

## Question Format

Number every question. For each option, lead with the letter. Indent options under the question. Always provide a recommended answer marked with `→`.

```
1. What is the primary goal of this project?
   A. Solve a specific user pain point
   B. Replace an existing internal tool
   C. Explore a new market opportunity
   D. Reduce operational cost or manual work
   E. Other: [please specify]
   F. None of the above (regenerate question with different options)

   → Recommended: A — most impactful projects start with a clear pain point

2. Who is the primary user?
   A. End consumers (B2C)
   B. Internal team members
   C. Developers / technical users
   D. Business clients (B2B)
   E. Other: [please specify]
   F. None of the above (regenerate question with different options)

   → Recommended: depends on context — answer what's true for your project
```

Users can respond with shorthand: `1A, 2C, 3B, 4E: [custom], 5F`.

---

## Process

### 1. Analyze Known Context

Read everything available: user's description, any existing `.specs/` files, CLAUDE.md, README. Extract what is already decided vs. genuinely unknown.

Do NOT ask questions whose answers are already clear from context.

### 2. Map Decision Dependencies

Identify which unknowns block other unknowns. Build a dependency graph mentally:

```
goal → target_user → scope → constraints → success_criteria → feature_decisions
```

Generate questions in dependency order. Don't ask about feature details before goal and scope are clear.

### 3. Present Questions

Open with a one-sentence framing of what you understood so far, then present the questions. Example:

> "I understand you want to build [X]. Before writing the spec, I have [N] questions to make sure we're aligned on the fundamentals:"

### 4. Interpret Answers

After the user responds:
- Map shorthand answers (`1A`) to their text
- Note any "Other" answers and treat them as inputs for the next round
- Note any "F" (regenerate) answers and replace those questions in the next round

### 5. Decide: Continue or Write Spec

After each round, assess whether enough is resolved to write the spec:

- **Write spec now:** Goal, user, scope, and success criteria are clear. Proceed to [specify.md](specify.md).
- **Another round:** Critical unknowns remain that would force guessing in the spec. Present a focused follow-up round (only the unresolved questions, minimum 3).

Never do more than **3 rounds** without writing something. If ambiguity persists after 3 rounds, flag it explicitly and write the spec with open questions marked.

### 6. Transition to Spec

When the interview is complete:

> "I have enough to write the spec. Let me capture the decisions:
> - Goal: [answer]
> - User: [answer]
> - Scope: [answer]
> ...
>
> Proceeding to Specify."

Then run [specify.md](specify.md) using the interview answers as the clarified requirements. The user does NOT need to repeat themselves.

---

## Question Design Rules

- **Options must be concrete** — "Card-based layout" not "Option A style"
- **Each option must be mutually exclusive** — avoid overlap between choices
- **Option E** is always "Other: [please specify]" — captures anything not listed
- **Option F** is always "None of the above (regenerate question with different options)" — lets users reject a bad framing
- **Recommended answer** is always shown — agent takes a position, user can override
- **Never ask about implementation details** in the interview — that's Design's job
- **Never ask about things already decided** — don't waste rounds on known facts

---

## Tips

- **Dependency-first ordering is mandatory** — asking about UI before knowing the user type produces useless answers
- **Recommendations are opinionated** — pick the most common successful pattern for similar projects, explain why in one line
- **Shorthand responses are the goal** — format questions so "1A, 2C, 3B" is a complete, meaningful response
- **Scope creep guard** — if an answer reveals a much larger project, flag it before continuing: "That sounds bigger than [X] — should we reframe the scope first?"
- **Interview fills context.md** — document key decisions in `.specs/features/[feature]/context.md` or `.specs/project/PROJECT.md` after the interview completes
