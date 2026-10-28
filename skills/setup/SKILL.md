---
name: setup
description: Configure a repo for the devloop skills. Adds a "## Workflow" section to AGENTS.md and keeps .desk/ out of git. Run once per repo before the other devloop skills.
disable-model-invocation: true
---

# Setup

Scaffold the per-repo configuration the devloop skills assume:

- **Checks**: the exact commands that prove the code works (test, typecheck, lint, build)
- **Base branch**: what feature branches are cut from and reviewed against
- **Desk**: `.desk/` holds specs, tickets, review notes and checkpoints. It is never committed.
- **Domain docs**: `CONTEXT.md` and `docs/adr/`, which _are_ committed

This is a prompt-driven skill, not a script. Explore, present what you found, confirm with the user, then write.

## Process

### 1. Explore

Read whatever exists; don't assume:

- `git remote -v`, `git branch -a`, and the default branch (`git symbolic-ref refs/remotes/origin/HEAD`, falling back to `main`/`master`). Is there a `develop` or `staging` branch too?
- `AGENTS.md` and `CLAUDE.md` at the repo root: does either exist? Is there already a `## Workflow` section?
- Build and test config: `package.json` scripts, `Makefile`, `pyproject.toml`, `Cargo.toml`, `go.mod`, CI workflow files. CI is the best source for the commands that actually gate a merge.
- `CONTEXT.md`, `CONTEXT-MAP.md`, `docs/adr/`
- `.desk/` and `.git/info/exclude`: already set up?
- `.scratch/`: leftovers from the old Pocock skills. Offer to rename it to `.desk/`; never delete it silently.

### 2. Present findings and ask

Summarise what's present and what's missing, then confirm, one item at a time, leading each with your recommendation so the user can accept it in a word. Use a structured question tool (e.g. `ask_user_question` in pi, `AskUserQuestion` in Claude Code) when available; otherwise ask in plain text.

- **Checks.** Propose the commands found, in the order they should run. If none were found, ask; never invent one.
- **Base branch.** Propose the default branch. If `develop` exists, ask which one features branch from, and note that hotfixes always branch from the production branch (usually `main`).
- **Production branch.** Only if it differs from the base branch.

### 3. Write

**Write to `AGENTS.md`**, creating it if needed. It's the file most agents read.

- If `CLAUDE.md` also exists, Claude Code reads it instead of `AGENTS.md`. Add a line `@AGENTS.md` to `CLAUDE.md` (if it isn't there) so Claude Code imports it. Show the user this edit before making it.
- If a `## Workflow` section exists in `CLAUDE.md` from an earlier setup, move it to `AGENTS.md` and leave the `@AGENTS.md` import in its place.

If a `## Workflow` section already exists in `AGENTS.md`, update it in place. Don't touch the surrounding sections.

The section:

```markdown
## Workflow

Uses the devloop skills (setup, brainstorm, design, write-spec, write-tickets, implement, review, hotfix, checkpoint, pickup).

### Checks

Run all of these, in order, before claiming work is done:

1. `<command>`
2. `<command>`

### Branches

- Base branch: `<base>` (features branch from and are reviewed against this)
- Production branch: `<prod>` (hotfixes branch from this, then back-merge into `<base>`)

### Desk

Work-in-progress files live in `.desk/<branch>/` (branch name with `/` replaced by `-`). Never committed.

- `spec.md`: the spec, ending in an acceptance checklist
- `tickets/NN-<slug>.md`: optional tickets, each with a `**Status:**` line
- `review.md`: review findings, deleted after fixing
- `checkpoint.md`: mid-work note, deleted on pickup

### Domain docs

`CONTEXT.md` (glossary) and `docs/adr/` (decisions) are committed. Read them before exploring; use the glossary's terms.
```

Then keep `.desk/` out of git: append `.desk/` to `.git/info/exclude` if it isn't already there. Use `.git/info/exclude` rather than `.gitignore` so no shared file changes. If `.desk/` is already tracked (`git ls-files .desk`), tell the user instead of untracking it.

### 4. Done

Tell the user what was written, and to reload or restart their agent so the new Workflow section is picked up (`/reload` in pi; restart Claude Code). They can edit the Workflow section by hand later; re-running this skill is only needed to start over.
