# Phase 1: Foundation

## Step 1: Understand the project

Existing or greenfield?

Look for: README, package.json, pyproject.toml, src/, any code.
- **Existing:** Brief summary. "Next.js, TypeScript, ESLint, Vitest." Continue.
- **Greenfield:** Read `../shipwright/skills/greenfield.md`. Return here after scaffold.

## Step 2: Makefile

Read `../shipwright/skills/makefile.md`.

Propose Makefile wrapping existing tools. Explain FILES pattern briefly.
If Makefile exists, propose improvements. Wait for approval.

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

## Shipwright
Skills: ../shipwright/skills/
```

## Step 4: Developer Profile

If Brain available: `brain.recall_developer_profile()` handles everything.
If no Brain: check `.claude/developers/{whoami}.md`, create if missing.
Read `../shipwright/skills/developer-profile.md`.

## Step 5: Get to Know the Developer

Read `../shipwright/skills/developer-discovery.md` and `../shipwright/community-insights.md`.

Quick conversation — biggest pain point, work style.
Use answers to recommend Phase 2 and 3 options.
Enrich developer profile with observations.

## Step 6: Summary

What was set up. One concrete example. Suggest next phase based on discovery.

Record sync point silently:
```bash
git -C ../shipwright rev-parse HEAD
```
Save to `../shipwright/local/projects/{project-name}/last-sync.md`.

---

## Next

Continue to `../shipwright/phases/02-knowledge.md`.
