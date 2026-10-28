---
name: hotfix
description: "Fix a production bug: confirm the branch is cut from production, find the root cause, add a failing regression test, apply the smallest fix, and finish with a back-merge checklist. Use for bugs users are hitting now, not for feature work."
disable-model-invocation: true
---

# Hotfix

You're finding a cause, not designing. No brainstorm, spec or tickets. Notes go in `.desk/<branch>/notes.md`.

## 1. Stop the bleeding (the user's call)

If users are blocked and the cause is unknown, ask whether the last deploy has been rolled back. Rolling back is the user's decision, never yours: don't run deploy or rollback commands.

## 2. Check the branch

Read the production branch from the repo's `## Workflow` section (default `main`).

- The current branch must be a `hotfix/<bug>` branch cut from an up-to-date production branch: `git merge-base --is-ancestor origin/<prod> HEAD` succeeds, and `git log origin/<prod>..HEAD` shows no commits other than this fix.
- If not, stop and give the user the commands: `git switch <prod> && git pull && git switch -c hotfix/<bug>`. Don't cut from `develop` or `staging`: they hold unreleased work.
- Uncommitted changes from other work? Stop and tell the user before doing anything.

## 3. Find the root cause

Follow the `debug` skill in full. All four phases apply; production pressure is exactly when its Iron Law matters most.

- Start from what the user gave you: what users see, the error, and real production logs. If they gave none, ask for them: the logs are facts you can't look up yourself.
- Record the symptom, the evidence and the confirmed root cause in `.desk/<branch>/notes.md` as you go.

## 4. Fix test-first

Follow the `implement` skill's rules of the loop, with these hotfix limits:

1. **Regression test first.** It must fail on the current code for the same reason users see. Show the failing output.
2. **Smallest fix** that makes it pass. **No refactors**, no "while I'm here" improvements, no dependency bumps unless the bug is in the dependency. Note anything else you'd change under "Follow-ups" in `notes.md`.
3. **Prove it**, as the `verify` skill requires: revert the fix, the test fails; restore the fix, the test passes. Then run every command under **Checks**.

Propose a commit (stage the test and the fix, never `.desk/`) and stop. Commit only when the user asks.

## 5. Hand off

Tell the user the next steps:

1. **Review** in a fresh session: `review`, then `review fix`.
2. **Ship**: a PR to `<prod>`, deploy, and confirm the error is gone from the production logs.
3. **Back-merge**: merge `<prod>` into each other long-lived branch (e.g. `staging`, `develop`), or the next release brings the bug back.

If you have a task-list tool (e.g. `todo` in pi, `TodoWrite` in Claude Code), add these three as tasks. Don't push, open the PR, deploy or merge yourself unless the user asks.
