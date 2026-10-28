---
name: checkpoint
description: Save where you are mid-work to .desk/<branch>/checkpoint.md so a fresh session can continue with the pickup skill. Use when stopping mid-ticket, when a session is getting long or forgetful, or when debugging has piled up failed attempts.
disable-model-invocation: true
---

# Checkpoint

Write a short note that lets a fresh agent continue this work. The note is for a machine that has read nothing of this conversation.

## When it's not needed

If the current ticket (or spec item) is finished and committed, there's nothing to checkpoint: the ticket status and the commits already say it. Tell the user to start a fresh session and run `implement` on the next ticket. Write a checkpoint only when the user insists.

## Where

`.desk/<branch>/checkpoint.md` for the current branch (`git branch --show-current`, `/` replaced by `-`). One checkpoint per branch: overwrite an older one, folding in anything from it that's still true.

## What to write

Don't duplicate what's already in other artifacts (spec, tickets, ADRs, `CONTEXT.md`, commits, the diff): reference them by path instead.

```markdown
# Checkpoint: <branch>

Written: <date and time>
Working on: <path to the ticket or spec>, item "<acceptance item in progress>"

## Done this session
- <what was finished, with commit hashes if committed>

## In progress
- <what's half-done, and where: files touched, uncommitted work (see `git status`)>

## Decisions made
- <decisions taken in conversation that aren't in the spec or an ADR yet, with the reason>

## Tried and failed
- <approaches that didn't work and why, so they're not retried> (debugging especially)

## Next step
<the single next action, concrete enough to start without asking>

## Suggested skills
- <e.g. `implement ticket 3`, or `debug`>
```

Leave out empty sections. Keep it short: a page, not a transcript.

Redact anything sensitive: API keys, passwords, tokens, personal data.

If the user passed an argument, treat it as what the next session will focus on and tailor the note to it.

## Finish

Tell the user:

1. The note's path.
2. What's uncommitted (from `git status`), and whether they want to commit it first.
3. How to continue: start a fresh session and run `pickup`. Or reopen this session later and carry on without `pickup`. Fresh session: `/new` in pi, `/clear` in Claude Code. Reopen: `/resume` in either.
4. If the agent can name sessions, suggest a name like `feat-x-03` so this session is easy to find later.
