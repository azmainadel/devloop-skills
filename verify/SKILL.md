---
name: verify
description: Use when about to claim work is complete, fixed, or passing, before committing or creating PRs - requires running verification commands and confirming output before making any success claims; evidence before assertions always
---

# Verification Before Completion

## Overview

**Core principle:** Evidence before claims, always.

**Violating the letter of this rule is violating the spirit of this rule.**

## The Iron Law

```
NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE
```

If you haven't run the verification command in this message, you cannot claim it passes.

## The Gate Function

```
BEFORE claiming any status or expressing satisfaction:

1. IDENTIFY: What command proves this claim?
2. RUN: Execute the FULL command (fresh, complete)
3. READ: Full output, check exit code, count failures
4. VERIFY: Does output confirm the claim?
   - If NO: State actual status with evidence
   - If YES: State claim WITH evidence
5. ONLY THEN: Make the claim

Skip any step = lying, not verifying
```

## Common Failures

| Claim | Requires | Not Sufficient |
|-------|----------|----------------|
| Tests pass | Test command output: 0 failures | Previous run, "should pass" |
| Linter clean | Linter output: 0 errors | Partial check, extrapolation |
| Build succeeds | Build command: exit 0 | Linter passing, logs look good |
| Bug fixed | Test original symptom: passes | Code changed, assumed fixed |
| Regression test works | Red-green cycle verified | Test passes once |
| Agent completed | VCS diff shows changes | Agent reports "success" |
| Requirements met | Line-by-line checklist | Tests passing |

## Red Flags - STOP

- Using "should", "probably", "seems to"
- Expressing satisfaction before verification ("Great!", "Perfect!", "Done!", etc.)
- About to commit/push/PR without verification
- Trusting agent success reports
- Relying on partial verification
- Thinking "just this once"
- Tired and wanting work over
- **ANY wording implying success without having run verification**

## Rationalization Prevention

| Excuse | Reality |
|--------|---------|
| "Should work now" | RUN the verification |
| "I'm confident" | Confidence ≠ evidence |
| "Just this once" | No exceptions |
| "Linter passed" | Linter ≠ compiler |
| "Agent said success" | Verify independently |
| "I'm tired" | Exhaustion ≠ excuse |
| "Partial check is enough" | Partial proves nothing |
| "Different words so rule doesn't apply" | Spirit over letter |

## Key Patterns

**Tests:**
```
✅ [Run test command] [See: 34/34 pass] "All tests pass"
❌ "Should pass now" / "Looks correct"
```

**Regression tests (TDD Red-Green):**
```
✅ Write → Run (pass) → Revert fix → Run (MUST FAIL) → Restore → Run (pass)
❌ "I've written a regression test" (without red-green verification)
```

**Build:**
```
✅ [Run build] [See: exit 0] "Build passes"
❌ "Linter passed" (linter doesn't check compilation)
```

**Requirements:**
```
✅ Re-read plan → Create checklist → Verify each → Report gaps or completion
❌ "Tests pass, phase complete"
```

**Agent delegation:**
```
✅ Agent reports success → Check VCS diff → Verify changes → Report actual state
❌ Trust agent report
```

## Which Commands

Run every command under **Checks** in `.desk/config.md`, in order, before any completion claim. If the file doesn't exist yet, create it first (see **Repo config** below).

If you have a diagnostics tool (e.g. `lens_diagnostics` in pi, or an IDE/LSP diagnostics tool), also run it on the files you changed. It catches type and lint errors the test suite can miss, but it doesn't replace the Checks.

Show the output, or the relevant part of it with the exit code, in your message. A claim without the output next to it is not verified.

## Repo config

The devloop skills keep per-repo settings in `.desk/config.md`. It's local to this clone and never committed, so teammates who don't use these skills never see it:

```markdown
# devloop config

## Checks

1. `<command>`
2. `<command>`

## Branches

- Base: `<base>` (features branch from and are reviewed against this)
- Production: `<prod>` (hotfixes branch from this)
```

If the file exists, use it. If it's missing, build it with the steps below. A repo set up by the old `setup` skill has a `## Workflow` section in `AGENTS.md` or `CLAUDE.md`. If you find one, start from its Checks and Branches.

1. **Checks.** Find the commands that gate a merge. CI workflow files are the best source, then `package.json` scripts, `Makefile`, `pyproject.toml`, `Cargo.toml`, `go.mod`. List them in the order they should run. If you find none, ask; never invent one.
2. **Branches.** Production is the remote's default branch (`git symbolic-ref --short refs/remotes/origin/HEAD`, minus the `origin/` prefix), falling back to `main` or `master`. If a `develop` branch exists (`git branch -a`), ask whether features branch from it. Otherwise the base is the production branch.
3. **Confirm** what you found with the user, leading with your proposal so they can accept it in a word. Use a structured question tool (e.g. `ask_user_question` in pi, `AskUserQuestion` in Claude Code) when available.
4. **Save** it to `.desk/config.md`. If `.desk/.gitignore` doesn't exist, create it first, containing just `*`, so git ignores everything in `.desk/`.

This happens once per clone. If a saved command later stops working because it was renamed or removed, say so and update the file with the user.

## When To Apply

**ALWAYS before:**
- ANY variation of success/completion claims
- ANY expression of satisfaction
- ANY positive statement about work state
- Committing, PR creation, task completion
- Moving to next task
- Delegating to agents

**Rule applies to:**
- Exact phrases
- Paraphrases and synonyms
- Implications of success
- ANY communication suggesting completion/correctness
