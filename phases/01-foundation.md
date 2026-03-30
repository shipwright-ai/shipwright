# Phase 1: Foundation

## Ground Rules

**Every change must be discussed with the developer first.** Never modify files
based on assumptions. Propose what you want to change, explain why, and wait for
approval. This applies to every step below.

**Explain the value.** For each step, tell the developer what problem it solves
and what they get out of it. Not everyone knows why a Makefile or CLAUDE.md matters.
Speak in terms of their daily workflow, not abstract methodology.

**Never skip steps.** Even if something seems obvious. Each step builds on the previous
one. If the developer wants to skip something, that's their call — but you must
present it first.

**Preserve existing work.** When modifying CLAUDE.md, Makefile, or any existing file,
READ IT FIRST. Add sections -- never overwrite what's already there. The developer's
existing setup is intentional.

**Plan mode for additions.** When the developer proposes changes, additions, or
customizations during onboarding, switch to plan mode first. Discuss the scope,
agree on the approach, then implement. Never go straight to code when the developer
brings something new to the table.

## Progress Tracking

After completing each step, silently update `../shipwright/local/projects/{project-name}/last-sync.md` with current progress. This enables resume if the session is interrupted.

Get the current SHA once at the start:
```bash
git -C ../shipwright rev-parse HEAD
```

After each step, write/update last-sync.md:
```markdown
# Last Sync

project: {project-name}
shipwright_sha: {sha}
synced_at: {today}
status: in-progress
project_type: {greenfield|existing}
phase: 1
step: {last completed step number}
completed_steps:
  - "step 1: project understanding"
notes: {what was done so far, key decisions}
```

The `project_type` field is critical -- greenfield and existing projects have different
resume paths (greenfield may need to continue scaffolding via greenfield.md).

## Step 1: Understand the project

**What to tell the developer:**
> "Let me understand your project first so everything I set up fits your actual workflow."

Existing or greenfield?

Look for: README, package.json, pyproject.toml, src/, any code.
- **Existing:** Read the codebase. Summarize what you found: stack, structure, tooling,
  testing, CI. Ask the developer: "Is this accurate? Anything I'm missing or got wrong?"
  Wait for confirmation before proceeding.
- **Greenfield:** Read `../shipwright/skills/greenfield.md`. Return here after scaffold.

**Checkpoint:** Create last-sync.md with step: 1. Also inject a minimal breadcrumb
into the project's CLAUDE.md (APPEND, don't overwrite):

```
## Shipwright
Onboarding in progress. To resume: read ../shipwright/setup.md
Skills: ../shipwright/skills/
```

This ensures any new session knows shipwright exists and where to resume.

**Save progress ->** step: 1

## Step 2: Makefile

Read `../shipwright/skills/makefile.md`.

**What to tell the developer:**
> "A Makefile gives you one consistent interface for all common tasks -- lint, test,
> dev, build. Instead of remembering different commands for each tool, you and Claude
> both use `make test`, `make lint`, etc. It also means Claude doesn't have to guess
> how to run things in your project."

If Makefile exists: read it, propose specific improvements. Show the diff.
If no Makefile: propose one based on the project's actual tools.

**Discuss:** Present the proposed Makefile and explain each target. Ask:
"Does this cover your workflow? Anything missing? Anything you'd change?"
Wait for approval before writing.

**Save progress ->** step: 2

## Step 3: CLAUDE.md

Read `../shipwright/skills/claude-md-optimization.md`.

**What to tell the developer:**
> "CLAUDE.md is instructions that Claude reads at the start of every session.
> Right now Claude guesses how your project works -- this tells it explicitly.
> It also sets up a workflow where every feature gets tracked and you always
> approve the plan before I start coding."

**If CLAUDE.md already exists:** Read it carefully. DO NOT overwrite. Propose
ADDING the following sections. Show the developer exactly what you want to add
and where. Explain each section.

**If no CLAUDE.md:** Propose creating one. Walk through each section with the
developer.

Sections to add (discuss each one):

```
## Idea Capture
Before implementing any new feature or request:
1. Capture it as a Brain idea first -- never code before capturing
2. Refine the plan with the developer -- discuss, adjust, iterate until they approve
3. Only start implementing after developer says go
Read ../shipwright/skills/idea-capture.md for the full workflow.

## Post-Implementation
After completing a feature or fix:
1. Update the related Brain idea -- check off completed steps, add new ones if scope changed
2. Update context (Why/What/Who/How) if decisions were made during implementation
3. Run make lint and make test

## Shipwright
Skills: ../shipwright/skills/
```

**Explain the value of each section:**
- **Idea Capture:** "This means I won't start coding before you agree on the plan.
  Every feature is tracked so nothing falls through the cracks across sessions."
- **Post-Implementation:** "After I finish something, I update the tracker so you
  always know what's done and what's pending. No orphaned work."

**Important:** Update the `## Shipwright` breadcrumb from Step 1 to its final content.
Never remove existing sections the developer wrote.

**Write the feedback memory** -- this survives compaction and loads every session.
Explain to the developer:
> "I'm also writing a persistent memory so this workflow sticks across all sessions,
> even when conversation context gets compressed."

Create `.claude/memory/feedback_capture_before_code.md` in the project:

```markdown
---
name: capture before code
description: Never implement without capturing to Brain first and getting developer approval
type: feedback
---

Always capture work as a Brain idea before implementing. Then refine the plan
with the developer until they approve. Never start coding without this step.

**Why:** Without capture, work goes untracked, duplicates happen across sessions,
and Claude starts coding before the developer agrees on scope.

**How to apply:** On any new feature, fix, or request: 1) brain.create_memory,
2) discuss plan with developer, 3) implement only after approval,
4) update the idea when done.
```

Also add a pointer to this file in `.claude/memory/MEMORY.md`:
```
- [Capture before code](feedback_capture_before_code.md) -- never implement without Brain idea + developer approval
```

**Save progress ->** step: 3

## Step 4: Context Compaction

Read `../shipwright/skills/context-compaction.md`.

**What to tell the developer:**
> "Long sessions lose context -- Claude forgets decisions made earlier as the
> conversation grows. Smart compaction means I summarize at natural break points
> (after commits, between tasks) so I stay sharp on the current work. Without this,
> you might notice me re-asking things or contradicting earlier decisions."

Discuss with the developer:
- If they work in long sessions -> recommend both hook + CLAUDE.md rule
- If they work in short focused sessions -> mention it exists, skip setup
- Let developer choose. Don't force it.

**Save progress ->** step: 4

## Step 5: Developer Profile

If Brain available: `brain.recall_developer_profile()` handles everything.
If no Brain: check `.claude/developers/{whoami}.md`, create if missing.
Read `../shipwright/skills/developer-profile.md`.

**What to tell the developer:**
> "This helps me adapt to how you work -- whether you prefer terse responses or
> detailed explanations, whether you like to review every change or trust me to
> proceed. It builds up over time so I get better at working with you specifically."

**Save progress ->** step: 5

## Step 6: Get to Know the Developer

Read `../shipwright/skills/developer-discovery.md` and `../shipwright/community-insights.md`.

**What to tell the developer:**
> "Quick questions to understand your workflow so I can suggest what else might help."

Quick conversation -- biggest pain point, work style.
Use answers to recommend Phase 2 and 3 options.
Enrich developer profile with observations.

**Save progress ->** step: 6

## Step 7: Summary

What was set up. One concrete example of how the workflow works now.
Suggest next phase based on discovery.

Mark onboarding complete in last-sync.md:
```markdown
# Last Sync

project: {project-name}
shipwright_sha: {sha}
synced_at: {today}
status: complete
project_type: {greenfield|existing}
phase: 1
step: 7
completed_steps:
  - "step 1: project understanding"
  - "step 2: makefile"
  - "step 3: claude.md"
  - "step 4: context compaction"
  - "step 5: developer profile"
  - "step 6: discovery"
  - "step 7: summary"
notes: {summary of what was set up and key decisions}
```

---

## Next

Continue to `../shipwright/phases/02-knowledge.md`.
