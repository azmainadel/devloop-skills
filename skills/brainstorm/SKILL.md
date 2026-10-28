---
name: brainstorm
description: Interview the user relentlessly to stress-test a plan, decision, or idea, one round of questions at a time. Writes no files. Use when the user wants to think something through, stress-test their thinking, or uses any 'grill' or 'brainstorm' trigger phrases. If they're about to build it and want docs, use design instead.
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

This skill writes no files. If the user wants the outcome recorded as a glossary and decisions, suggest the `design` skill instead.

## Rounds

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

## Facts vs decisions

Finding _facts_ is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, tools, etc.), dispatch a sub-agent to find it, or look it up yourself if you have no sub-agents; don't ask the user for anything you could look up yourself. Don't block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait for the answer; ask the rest of the frontier now. The _decisions_ are the user's: put each to them and wait.

## Done

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on it until the user confirms you have reached a shared understanding.

End with a short summary of the decisions reached. If the user is going to build it, suggest `write-spec` next (or `design` first, if the domain language or decisions are worth recording).
