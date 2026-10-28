---
name: pickup
description: Continue work in a fresh session: read .desk/<branch>/checkpoint.md, git status and the ticket or spec status, then name the next step. Use at the start of a session to answer "where was I?".
disable-model-invocation: true
---

# Pickup

Work out where things stand on the current branch and name the next step. Don't start the work itself until the user confirms.

## 1. Gather the state

Find the desk folder for the current branch (`git branch --show-current`, `/` replaced by `-`): `.desk/<branch>/`. If the user passed a different path, use that.

Read, in this order:

1. `checkpoint.md`, if present: the note from the last session.
2. `git status` and `git log --oneline <base>..HEAD` (base from the repo's `## Workflow` section, else `main`): what's committed and what isn't.
3. `tickets/*.md`, if present: each ticket's `**Status:**`, blockers and unticked acceptance items. Otherwise, the unticked items in `spec.md`'s Acceptance list.
4. `review.md`, if present: review findings not yet fixed.
5. `notes.md`, if present: hotfix notes.

No desk folder and no checkpoint? Say so, and suggest `write-spec` (or `setup`, if the repo has no `## Workflow` section).

## 2. Reconcile

Trust the repo over the note. If the checkpoint says something is in progress but the commits show it finished, or the ticket statuses disagree with the work on disk, say so and go with what git and the files show.

## 3. Report

Keep it short:

```
Branch:    feat/team-billing
Working on: tickets/03-invoice-totals.md (in-progress, 2/4 items ticked)
Uncommitted: src/billing/invoice.ts, test/billing/invoice.test.ts
Last session: <one line from the checkpoint>
Next:      <the single next action>
Run:       implement ticket 3
```

Then the checkpoint's **Decisions made** and **Tried and failed**, if any, since the next session must respect them.

## 4. Clean up

Once the user confirms the summary is right, delete `checkpoint.md`: its content now lives in this conversation, and a stale note would mislead the next pickup. If the user wants to keep it, leave it.
