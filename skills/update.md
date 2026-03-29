# Skill: Update

## Purpose

When shipwright gets updated by other developers (git pull brings new commits),
figure out what changed and whether it matters for this developer and their project.

## How It Works

Each project onboarded by shipwright stores the last seen shipwright commit SHA in:
`../shipwright/local/projects/{project-name}/last-sync.md`

This file is gitignored — it's local to each developer.

### Format of last-sync.md

```markdown
# Last Sync

project: my-app
shipwright_sha: abc123f
synced_at: 2025-03-28
notes: Initial onboarding — makefile, CLAUDE.md, pre-commit hooks
```

## When to Run

At the start of any session where the developer is working on a project
that has been onboarded with shipwright, check for updates:

1. Read `../shipwright/local/projects/{project-name}/last-sync.md`
2. If it doesn't exist, skip — this project hasn't been onboarded yet
3. Get the stored SHA
4. Get the current shipwright HEAD: `git -C ../shipwright rev-parse HEAD`
5. If they match, nothing changed — proceed normally
6. If they differ, run the diff

## Checking What Changed

```bash
git -C ../shipwright log --oneline {stored_sha}..HEAD
git -C ../shipwright diff --name-only {stored_sha}..HEAD
```

This tells you:
- Which files changed (skills, hooks, community-insights, setup.md)
- What the commit messages say about why

## Deciding What Matters

Read the actual diffs for changed files:

```bash
git -C ../shipwright diff {stored_sha}..HEAD -- skills/makefile.md
```

For each changed file, ask yourself:
- **Skill improved** — does this project use this skill? If yes, might need to update
  the project's Makefile, hooks, or CLAUDE.md to reflect new best practices.
- **New skill added** — could this developer benefit? Check their preferences.md
  and the discovery notes. Mention it if relevant, don't push if not.
- **Hook improved** — is this hook active in the project? If yes, may need updating.
- **Community insight added** — informational, mention if relevant to their pain points.
- **setup.md changed** — no action needed for already-onboarded projects unless
  the change addresses something the project is missing.

## Talking to the Developer

Keep it brief. Don't dump a changelog. Filter to what matters for them:

"Shipwright has been updated since we last synced. A few things that affect you:"

Only mention changes that are relevant. Skip everything else.

For each relevant change, explain:
- What changed and why
- Whether it affects their project
- What you'd recommend updating (if anything)
- Ask before making any changes

Example:
"The makefile skill now recommends a `make clean` target for build artifacts.
Your project doesn't have one — want me to add it?"

Example:
"A new community insight was added about TypeScript strict mode catching bugs earlier.
Your project has strict mode off — worth considering but not urgent."

## After Syncing

Update the last-sync.md with the current SHA:

```markdown
# Last Sync

project: my-app
shipwright_sha: {current_sha}
synced_at: {today}
notes: Updated makefile with clean target. Skipped strict mode change — developer decided not now.
```

The notes field is important — it's a mini-changelog of what was applied and what was
deliberately skipped. This prevents re-suggesting things the developer already said no to.

## If the SHA Is Very Old

If there are many commits between the stored SHA and HEAD (say 20+), don't walk
through every change. Instead:

1. Summarize the major changes at a high level
2. Focus on the 2-3 most impactful ones for this project
3. Offer to do a full re-onboarding if the changes are substantial:
   "A lot has changed — want me to re-run the full setup to make sure
   your project has the latest best practices?"
