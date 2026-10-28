---
name: implement
description: Build a ticket or a spec test-first, one vertical slice at a time (red → green), tracking progress in the ticket's Status line or the spec's acceptance checkboxes. Use when the user wants to build features or fix bugs test-first, mentions "red-green-refactor", or passes a .desk ticket or spec path.
---

# Implement

Build one unit of work (a ticket, or a spec without tickets) with the red → green loop, then stop and propose a commit. This skill never commits, pushes or opens PRs: the user asks for each.

## Before you start

### 1. Find the work

- **Ticket number or name** (`3`, `ticket 3`, `invoice totals`): match it against `.desk/<branch>/tickets/`. If more than one ticket matches, ask.
- **`spec`**: use `.desk/<branch>/spec.md`.
- **Path given** (e.g. another branch's desk file): use it.
- **Nothing given**: look in `.desk/<branch>/` for the current branch (`git branch --show-current`, `/` replaced by `-`). If `tickets/` exists, take the lowest-numbered ticket that isn't `done` and whose blockers are all `done`. Otherwise use `spec.md`.
- **Nothing found** (no desk, or the user describes the work inline): work from the request, and ask the seam question below.

Tell the user which file you're working from before writing any code.

### 2. Read the context

- The ticket's **What to build** and **Acceptance**, or the spec's **Acceptance** checklist.
- The spec's **Testing Decisions**. Its **Agreed seams** are already confirmed: test at them without asking again. (For a ticket, read the spec in the same desk folder.)
- `CONTEXT.md` (if it exists), so test names and interface vocabulary match the project's domain language, and the ADRs in the area you're touching.
- The **Checks** in the repo's `## Workflow` section.

### 3. Size check (spec only)

Working straight from a spec only works if it fits in about one session. If the spec has many unchecked acceptance items or touches several areas, warn: "This looks like 3+ sessions — run `write-tickets` first, or continue and use `checkpoint`/`pickup`." Don't refuse; the user decides.

### 4. Mark it started

- **Ticket**: set `**Status:** in-progress`.
- **Track progress**: if you have a task-list tool (e.g. `todo` in pi, `TodoWrite` in Claude Code), add one task per unchecked acceptance item and work them in order.

## What a good test is

Tests verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't. A good test reads like a specification: "user can checkout with valid cart" tells you exactly what capability exists, and it survives refactors because it doesn't care about internal structure.

See [tests.md](tests.md) for examples and [mocking.md](mocking.md) for mocking guidelines.

## Seams: where tests go

A **seam** is the public boundary you test at: the interface where you observe behavior without reaching inside. Tests live at seams, never against internals.

**Test only at pre-agreed seams.** The spec's Agreed seams count as agreed. If there's no spec, or a test needs a seam the spec doesn't list, write down the seams under test and confirm them with the user first. No test is written at an unconfirmed seam. You can't test everything, so agreeing the seams up front is how testing effort lands on the critical paths and complex logic instead of every edge case.

Ask: "What's the public interface, and which seams should we test?"

## Anti-patterns

- **Implementation-coupled**: mocks internal collaborators, tests private methods, or verifies through a side channel (querying the database instead of using the interface). The tell: the test breaks when you refactor but behavior hasn't changed.
- **Tautological**: the assertion recomputes the expected value the way the code does (`expect(add(a, b)).toBe(a + b)`, a snapshot derived by hand the same way, a constant asserted equal to itself), so it passes by construction and can never disagree with the code. Expected values must come from an independent source of truth: a known-good literal, a worked example, the spec.
- **Horizontal slicing**: writing all tests first, then all implementation. Bulk tests verify _imagined_ behavior: you test the _shape_ of things rather than user-facing behavior, the tests go insensitive to real changes, and you commit to test structure before understanding the implementation. Work in **vertical slices** instead: one test → one implementation → repeat, each test a **tracer bullet** that responds to what the last cycle taught you.

## Rules of the loop

- **Red before green.** Write the failing test first, then only enough code to pass it. Don't anticipate future tests or add speculative features.
- **One slice at a time.** One seam, one test, one minimal implementation per cycle.
- **Refactoring is not part of the loop.** Note refactor ideas as you go and hand them to the `review` skill, which has a section for them. Don't refactor mid-loop.
- **Before calling a slice green**, run the relevant tests and, if you have a diagnostics tool (e.g. `lens_diagnostics` in pi, or an IDE/LSP diagnostics tool), check the files you changed. A test that passes alongside new type errors isn't green.
- **A test fails for a reason you don't understand?** Stop guessing and switch to the `debug` skill.

## After each acceptance item

- Tick it: `- [x]` in the ticket's or spec's Acceptance list, and complete its task if you're tracking one.
- **Spec without tickets**: stop after each item and propose a commit (below), so every slice lands separately. Continue when the user says so.

## Finishing

1. Run every command under **Checks** in the `## Workflow` section and show the output. Follow the `verify` skill: no "done" without fresh evidence.
2. **Ticket**: set `**Status:** done` once every acceptance item is ticked and the checks pass. If anything is left, keep it `in-progress` and say what remains.
3. **Propose a commit**: list the files to stage (never `.desk/`) and a commit message: short imperative subject, body saying what changed and why. Then stop. Commit only when the user asks.
4. Name the next step: the next unblocked ticket, the next unchecked spec item, or `review` (in a fresh session) when everything is done.
