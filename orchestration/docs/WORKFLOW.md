# Shipwright Orchestration — Workflow

## Overview

```
CAPTURE → PLAN → APPROVE → EXECUTE → DOCUMENT
                    ↑                      │
                    └──────────────────────┘
                    (docs feed next planning)
```

Gate configuration (D031) controls autonomy at each step:
- Manual: developer approves at every gate
- Semi-auto: developer approves plans, execution + review run autonomously
- Full-auto: all gates self-resolve, hard stops only (BLOCKED, SECURITY, DESTRUCTIVE)

## 1. Capture Ideas

Developer writes ideas — in conversation, in a file, through brain UI. Can be batch.

```
For each idea:
  brain.create_memory({
    title: "<idea>",
    kind: "ideas",
    tags: ["area/<x>", "persona/<y>"]
  })
  → Brain finds format guide: docs/format-guides/ideas/memory.md
  → Response: write 5W content to <path>
  → Write: Why, What, Who, When, How
  → Attach reference files if any:
    brain.attach_to_memory({ file_path: "<path>", memory_file: "<idea path>" })
```

Ideas can have attached files: sketches, samples, Figma exports, competitor screenshots, CSVs, anything relevant.

## 2. Plan (Sequential, One Idea at a Time)

### Concrete signal check (D024)

Before brainstorming, check if idea is already concrete:
- Has file paths, function names, error messages? → skip brainstorming, go straight to planning
- Vague? → brainstorm first with ambiguity scoring (D020)

### Find ideas to plan

```
brain.browse_memories({ kind: "ideas", status: "not-started" })
→ Show only top-level ideas (exclude plans and work-items)
→ Present list to developer
→ Developer picks which to plan and in what order
```

### Spawn planning team

```
Product Agent spawns (coordinator):
  brain.recall_agent_memory({ agent_name: "product" })
  brain.recall_developer_profile({})

Product Agent uses SendMessage to activate:
  → Critic (always)
  → UX (if idea involves frontend)
  → Tech Lead (if idea is architecturally complex)

Each advisor on activation:
  brain.recall_agent_memory({ agent_name: "<role>" })
```

### Plan each idea

```
For each idea (sequentially, finish before next):

  Read idea: Read("docs/ideas/<slug>/memory.md")
  Read attachments: Read any attached files referenced in idea

  Search related context:
    brain.search_memories({ query: "<topic>", kind: "decisions", tags: [...idea.tags...] })
    brain.search_memories({ query: "<topic>", kind: "agent-learnings", tags: [...idea.tags...] })
    brain.search_memories({ query: "<topic>", tags: ["plan"] })

  Consensus planning (D029):
    Short mode (default): 3-5 principles, 2+ approaches, max 5 iterations
    Deliberate mode (auto for auth/migrations/destructive): adds pre-mortem + expanded tests

  Challenge escalation (D023):
    Round 4+: Contrarian mode
    Round 6+: Simplifier mode
    Round 8+: Ontologist mode (if ambiguity > 30%)

  Create plan as sub-memory of idea:
    brain.create_memory({
      title: "<plan name>",
      kind: "ideas",
      tags: ["plan", ...idea.tags],
      parent: "docs/ideas/<slug>/memory.md"
    })
    → Brain finds format guide: docs/format-guides/ideas/plan/memory.md
    → Response: write plan content to <path>
    → Plan includes: 5W, approach, decisions, risks, task breakdown, critic findings

  Capture decisions:
    → Note in plan file (context)
    → Store standalone (source of truth):
      brain.create_memory({
        title: "Decision: <what>",
        kind: "decisions",
        tags: ["subsystem/<x>", "area/<y>", ...relevant tags],
        refs: [idea path, plan path]
      })

  Size check:
    Plan < 5 tasks → send next idea to same warm team
    Plan 5+ tasks or context heavy → present current, spawn fresh team

  Present plan to developer (HARD GATE — unless auto mode)
```

## 3. Approve

Developer reviews each plan. Presented format:

```
### Plan: <title>
Idea: <link to source idea>
Why: <one line>
Approach: <chosen approach and why>
Tasks:
  1. <task> — <file path> — <acceptance criteria>
  2. <task> — <file path> — <acceptance criteria>
  ...
Risks (from critic):
  - <risk> — <how addressed or explicitly rejected>
Decisions made:
  - <decision> — <rationale> — <ref to standalone decision>
```

Developer options:
- Approve → add tag: phase/approved
- Change → revise specific parts, re-present
- Reject → add tag: phase/rejected, note why
- Split → break into smaller plans
- Defer → add tag: phase/deferred

No execution until approved.

## 4. Spawn Work-items from Approved Plan

```
For each task in the plan:
  brain.create_memory({
    title: "<task name>",
    kind: "ideas",
    tags: ["work-item", "phase/planned", ...inherited tags],
    parent: "docs/ideas/<slug>/<plan-slug>/memory.md"
  })
  → Brain finds format guide: docs/format-guides/ideas/work-item/
  → Copies memory.md format + section templates:
     1_execution_developer.md
     2_review_reviewer.md
     3_documentary_documenter.md
  → Response: lists all created file paths
  → Write work-item content to memory.md
```

## 5. Execute

For each work-item:

```
Controller:
  brain.recall_agent_memory({ agent_name: "developer" })
  → Inject memories into spawn prompt

Spawn fresh subagent:
  Agent(prompt: """
  ## Your memories from past sessions
  <recalled memories>

  ## Your work item
  File: docs/ideas/<slug>/<plan>/<task>/memory.md
  Read it. Your tasks are the checkboxes.

  ## Your execution checklist
  File: docs/ideas/<slug>/<plan>/<task>/1_execution_developer.md
  Follow it. Check off items as you complete each.

  ## Rules
  - Read both files FIRST
  - Work through tasks in order
  - After completing each task:
    - [ ] Check off task in memory.md
    - [ ] Check off quality gate in 1_execution_developer.md
    - [ ] Run tests
    - [ ] If learned something non-obvious:
      Personal → append to <memory file path from recall>
      Project-wide → brain.create_memory({ kind: "agent-learnings", tags: [...] })
  - Report: DONE / DONE_WITH_CONCERNS / NEEDS_CONTEXT / BLOCKED
  """)
```

### Slop-cleaner pass (D025)

After execution, before review:
- Dead code deletion on changed files
- Duplication removal
- Naming cleanup
- Test reinforcement
- Quality gates (lint, format, typecheck)
- Regression test to verify nothing broke

### Two-stage review

```
Reviewer reads: 2_review_reviewer.md

Stage 1 — Spec compliance:
  Does code match what the work-item specified?
  Missing requirements? Unnecessary additions?

Stage 2 — Code quality:
  Separation of concerns, error handling, test coverage,
  architecture, performance, security.

Findings use severity levels:
  [CRITICAL] — must fix before merge
  [IMPORTANT] — should fix, may defer with reason
  [MINOR] — nice to have
  [NOTE] — observation

Verdict: PASS / PASS WITH NOTES / FAIL
```

### On work-item completion

```
- [ ] All tasks checked off in memory.md
- [ ] All items checked off in 1_execution_developer.md
- [ ] Review verdict recorded in 2_review_reviewer.md
- [ ] Progress rolls up automatically (work-item → plan → idea)
- [ ] Agent stores final learnings (quality gate: D027)
```

## 6. Document

```
Doc-writer reads: 3_documentary_documenter.md

  brain.recall_agent_memory({ agent_name: "doc-writer" })

  Search for affected docs using inherited tags:
    brain.search_memories({ query: "<what changed>", tags: [...from idea...] })

  Update affected:
    - Feature documentation
    - Architecture docs (if structural changes)
    - Decision records (if decisions validated/invalidated)

  Capture screenshots (if project has UI):
    brain.screenshot({ url: "<page>", memory_file: "<work-item path>", width: 375 })
    brain.screenshot({ url: "<page>", memory_file: "<work-item path>", width: 768 })
    brain.screenshot({ url: "<page>", memory_file: "<work-item path>" })

  Check off items in 3_documentary_documenter.md
```

## Free-ride Mode

For ad-hoc sessions (fixing bugs, adding small features, exploring):

```
- [ ] Developer describes what they're doing
- [ ] Create session idea:
  brain.create_memory({
    title: "Session: <topic>",
    kind: "ideas",
    tags: ["session", "type/free-ride"]
  })
- [ ] Work freely — fix, add, explore
- [ ] After each logical chunk:
  - [ ] Capture what changed and why in session file
  - [ ] Run tests
  - [ ] If decisions were made → store as standalone decisions
- [ ] At session end:
  - [ ] Doc-writer updates affected docs
  - [ ] Learnings stored (quality gate: D027)
  - [ ] If new ideas emerged → capture as ideas for future planning
```

## Agent Communication Summary

```
PLANNING:
  Orchestrator ──spawn──→ Product Agent (coordinator)
  Product Agent ──SendMessage──→ Critic
  Product Agent ──SendMessage──→ UX (if frontend)
  Product Agent ──SendMessage──→ Tech Lead (if complex)
  Advisors ──SendMessage──→ Product Agent (findings)
  Product Agent ──present──→ Developer (HARD GATE)

EXECUTION:
  Controller ──spawn (fresh)──→ Developer subagent (per work-item)
  Controller ──spawn (fresh)──→ Slop-cleaner (after execution)
  Controller ──spawn (fresh)──→ Spec reviewer
  Controller ──spawn (fresh)──→ Quality reviewer

DOCUMENTATION:
  Controller ──spawn──→ Doc-writer (after review passes)

ALL SPAWNS INCLUDE:
  brain.recall_agent_memory({ agent_name: "<role>" })
  brain.recall_developer_profile({})
```
