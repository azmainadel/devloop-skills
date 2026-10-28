---
name: design
description: Interview the user relentlessly to sharpen a plan or design they intend to build, and record the domain glossary (CONTEXT.md) and decisions (ADRs) as they're resolved. Use before write-spec.
disable-model-invocation: true
---

# Design

Interview the user relentlessly until you reach a shared understanding, and build the project's domain model as you go: challenge terms, invent edge-case scenarios, and write the glossary and decisions down the moment they crystallise.

Before the first round, read `CONTEXT.md` (or `CONTEXT-MAP.md`) and the ADRs in `docs/adr/` that touch the area, and explore the code the idea touches.

## The interview

Map the idea as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask the whole frontier in one round (subject to tool limits, below): number each question and give your recommended answer. Then wait for the user's answers before the next round.

If you have a structured question tool (e.g. `ask_user_question` in pi, `AskUserQuestion` in Claude Code), use it for the round:

- One tool question per frontier question. The header is the question title, and the options are the concrete choices.
- Put your recommended answer first and append "(Recommended)" to its label. Use the option description for the trade-off.
- Most tools cap a call at around 4 questions. If the frontier is bigger, ask the most foundational ones and carry the rest to the next round.
- Only open questions with no discrete choices go in the text format. Don't invent options just to fit the tool.

If no such tool is available, format the round like so:

```
❓ **Q1** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>
```

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Finding _facts_ is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, tools, etc.), dispatch a sub-agent to find it, or look it up yourself if you have no sub-agents; don't ask the user for anything you could look up yourself. Don't block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait for the answer; ask the rest of the frontier now. The _decisions_ are the user's: put each to them and wait.

## Domain modeling during the interview

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `CONTEXT.md`, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y. Which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account': do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible. Which is right?"

### Update CONTEXT.md inline

When a term is resolved, update `CONTEXT.md` right there. Don't batch these up: capture them as they happen. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

`CONTEXT.md` should be totally devoid of implementation details. Do not treat `CONTEXT.md` as a spec, a scratch pad, or a repository for implementation decisions. It is a glossary and nothing else.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse**: the cost of changing your mind later is meaningful
2. **Surprising without context**: a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off**: there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

If a decision contradicts an existing ADR, say so explicitly rather than silently overriding it: _"Contradicts ADR-0007 (event-sourced orders), but worth reopening because…"_

## File structure

Most repos have a single context:

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

If a `CONTEXT-MAP.md` exists at the root, the repo has multiple contexts. The map points to where each one lives:

```
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          ← system-wide decisions
├── src/
│   ├── ordering/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                 ← context-specific decisions
│   └── billing/
│       ├── CONTEXT.md
│       └── docs/adr/
```

Create files lazily: only when you have something to write. If no `CONTEXT.md` exists, create one when the first term is resolved. If no `docs/adr/` exists, create it when the first ADR is needed.

These files are committed project docs, not desk files. Don't put them in `.desk/`.

## Done

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on it until the user confirms you have reached a shared understanding.

End with a short summary: the decisions reached, the terms added to `CONTEXT.md`, and the ADRs written. Suggest `write-spec` next.
