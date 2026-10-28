---
name: write-spec
description: "Turn the current conversation into a spec at .desk/<branch>/spec.md, ending in an acceptance checklist: no interview, just synthesis of what you've already discussed. Use after brainstorm or design."
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec. Do NOT interview the user; just synthesize what you already know.

The repo's `## Workflow` section (in `AGENTS.md` or `CLAUDE.md`) describes the desk layout. If it's missing, tell the user to run the `setup` skill first.

## Process

1. **Find the desk folder.** Run `git branch --show-current` and replace `/` with `-`: `feat/team-billing` → `.desk/feat-team-billing/`. If you're on the base branch, ask the user for a branch name, then suggest `git switch -c <name>` before writing. If `spec.md` already exists there, ask before overwriting it.

2. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary (`CONTEXT.md`) throughout the spec, and respect any ADRs in the area you're touching.

3. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

   Check with the user that these seams match their expectations. These are the **agreed seams**: `implement` tests at them without asking again.

4. Write the spec using the template below to `.desk/<branch>/spec.md`.

5. Stop. Tell the user the path and ask them to read it. Next is either `write-tickets` (if the work spans several sessions) or `implement spec` (if it fits in about one).

<spec-template>

# <Feature title>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- **Agreed seams**: the seams confirmed in step 3, one per line
- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this spec.

## Further Notes

Any further notes about the feature.

## Acceptance

A checklist of observable behaviours that, together, mean the feature is done. Order them so each one builds on the ones above it: each item is one vertical slice that `implement` can build and verify on its own.

- [ ] <observable behaviour 1>
- [ ] <observable behaviour 2>

</spec-template>
