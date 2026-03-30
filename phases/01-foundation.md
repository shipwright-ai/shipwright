# Phase 1: Foundation

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

The `project_type` field is critical — greenfield and existing projects have different
resume paths (greenfield may need to continue scaffolding via greenfield.md).

## Step 1: Understand the project

Existing or greenfield?

Look for: README, package.json, pyproject.toml, src/, any code.
- **Existing:** Brief summary. "Next.js, TypeScript, ESLint, Vitest." Continue.
- **Greenfield:** Read `../shipwright/skills/greenfield.md`. Return here after scaffold.

**Checkpoint:** Create last-sync.md with step: 1. Also inject a minimal breadcrumb
into the project's CLAUDE.md (create or append):

```
## Shipwright
Onboarding in progress. To resume: read ../shipwright/setup.md
Skills: ../shipwright/skills/
```

This ensures any new session knows shipwright exists and where to resume.

**Save progress →** step: 1

## Step 2: Makefile

Read `../shipwright/skills/makefile.md`.

Propose Makefile wrapping existing tools. Explain FILES pattern briefly.
If Makefile exists, propose improvements. Wait for approval.

**Save progress →** step: 2

## Step 3: CLAUDE.md

Read `../shipwright/skills/claude-md-optimization.md`.

Explain: "CLAUDE.md teaches Claude Code about your project. Without it, it guesses."

If exists: audit for bloat (>40 lines?), propose gate pattern split.
If new: create lean with gates. Template:

```
# [Project Name]
[Description]. Stack: [stack].
Run: make lint | make test | make dev

## Context Gates
Before writing code → read docs/conventions.md
Before decisions → read docs/decisions.md

## Idea Capture
Before implementing any new feature or request:
1. Capture it as a Brain idea first — never code before capturing
2. Refine the plan with the developer — discuss, adjust, iterate until they approve
3. Only start implementing after developer says go
Read ../shipwright/skills/idea-capture.md for the full workflow.

## Post-Implementation
After completing a feature or fix:
1. Update the related Brain idea — check off completed steps, add new ones if scope changed
2. Update context (Why/What/Who/How) if decisions were made during implementation
3. Run make lint and make test

## Shipwright
Skills: ../shipwright/skills/
```

**Important:** Keep the `## Shipwright` section from Step 1. Update the "Onboarding in progress"
line to remove it — the section now has its final content.

**Write the feedback memory** — this survives compaction and loads every session.
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
- [Capture before code](feedback_capture_before_code.md) — never implement without Brain idea + developer approval
```

**Save progress →** step: 3

## Step 4: Context Compaction

Read `../shipwright/skills/context-compaction.md`.

Explain why smart compaction matters -- context fills up, auto-compression
loses important details. Offer to set up compaction at natural boundaries
(after commits, between tasks). Quick conversation:

- If developer works in long sessions → recommend both hook + CLAUDE.md rule
- If developer works in short focused sessions → mention it exists, skip setup
- Let developer choose

**Save progress →** step: 4

## Step 5: Developer Profile

If Brain available: `brain.recall_developer_profile()` handles everything.
If no Brain: check `.claude/developers/{whoami}.md`, create if missing.
Read `../shipwright/skills/developer-profile.md`.

**Save progress →** step: 5

## Step 6: Get to Know the Developer

Read `../shipwright/skills/developer-discovery.md` and `../shipwright/community-insights.md`.

Quick conversation — biggest pain point, work style.
Use answers to recommend Phase 2 and 3 options.
Enrich developer profile with observations.

**Save progress →** step: 6

## Step 7: Summary

What was set up. One concrete example. Suggest next phase based on discovery.

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
