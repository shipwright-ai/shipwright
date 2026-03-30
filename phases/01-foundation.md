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
Before implementing any new feature or request, capture it as a Brain idea first.
Then implement. Never code before capturing.
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

**Save progress →** step: 3

## Step 4: Developer Profile

If Brain available: `brain.recall_developer_profile()` handles everything.
If no Brain: check `.claude/developers/{whoami}.md`, create if missing.
Read `../shipwright/skills/developer-profile.md`.

**Save progress →** step: 4

## Step 5: Get to Know the Developer

Read `../shipwright/skills/developer-discovery.md` and `../shipwright/community-insights.md`.

Quick conversation — biggest pain point, work style.
Use answers to recommend Phase 2 and 3 options.
Enrich developer profile with observations.

**Save progress →** step: 5

## Step 6: Summary

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
step: 6
completed_steps:
  - "step 1: project understanding"
  - "step 2: makefile"
  - "step 3: claude.md"
  - "step 4: developer profile"
  - "step 5: discovery"
  - "step 6: summary"
notes: {summary of what was set up and key decisions}
```

---

## Next

Continue to `../shipwright/phases/02-knowledge.md`.
