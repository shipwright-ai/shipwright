# Shipwright Orchestration — Architecture

## System Overview

```
orchestration repo (generic, updatable via git pull)
├── skills/          → checklists with brain.tool() calls (teaching files, not templates)
├── agents/          → 5W archetypes, setup generates project-specific prompts
├── setup.md         → discovery + phased onboarding (re-runnable)
└── docs/            → vision, decisions, architecture (this folder)
        ↓
    setup reads skills, generates into project
        ↓
developer's project
├── .claude/agents/  → project-specific agent prompts (from archetypes)
├── CLAUDE.md        → references active skills
└── Brain docs/
    ├── ideas/       → ideas, plans (tag: plan), work-items (tag: work-item)
    ├── decisions/   → standalone decisions (outlive ideas)
    ├── agent-memories/    → per-agent learnings
    ├── agent-learnings/   → project-wide learnings
    └── format-guides/ideas/  → format guides + section templates
        ├── memory.md                    (raw idea format)
        ├── plan/memory.md               (plan format)
        └── work-item/                   (work-item format + sections)
            ├── memory.md
            ├── 1_execution_developer.md
            ├── 2_review_reviewer.md
            └── 3_documentary_documenter.md
```

## Core Components

### 1. Brain (foundation — already exists)

Brain provides ALL infrastructure. Orchestration adds no new servers, databases, or MCP tools.

**What Brain provides:**
- `brain.create_memory({ title, kind, tags, refs, parent })` — create files with frontmatter
- `brain.browse_memories({ kind, tags, status })` — find memories by kind/status
- `brain.search_memories({ query, kind, tags })` — semantic + keyword search
- `brain.screenshot({ url, memory_file, width })` — capture UI state (375=mobile, 768=tablet, default=desktop)
- `brain.attach_to_memory({ file_path, memory_file })` — attach any file to a memory
- `brain.recall_agent_memory({ agent_name })` — load agent's learnings from past sessions
- `brain.recall_developer_profile({})` — load developer communication preferences
- `brain.delete_memory({ memory_file })` — remove with ref cleanup

**What Brain does in responses (enforcement mechanism):**
- `create_memory` response includes checklist of NEXT STEPS — forces Claude to write content, attach files
- `browse/search` responses append CLAUDE_REMINDER — "update memories if scope changed"
- Duplicate detection BLOCKS creation and presents two forced choices
- `recall_agent_memory` returns file path for appending new learnings
- `recall_developer_profile` instructs agent to OBSERVE developer if profile is new

**Reading memories:** Just use `Read("docs/ideas/my-idea/memory.md")`. No Brain API needed to read a file on disk.

### 2. Skills (workflow engine)

Skills are markdown checklists that tell Claude exactly what to do. Each skill:
- Has a clear trigger (when to use it)
- Uses checkbox steps (trackable progress)
- References exact `brain.tool()` calls with parameters
- Includes hard gates where developer approval is required

Skills are NOT prose essays. They are recipes.

### 3. Agent Archetypes (role definitions)

Defined in orchestration repo using 5W format:
- **Who** — role identity
- **Why** — what goes wrong without this role
- **What** — specific responsibilities
- **When** — which workflow phases this agent participates in
- **How** — approach and constraints

Plus an **Operations** section with exact checklists and `brain.tool()` calls.

During setup, Claude reads archetypes and generates project-specific agent prompts into `.claude/agents/` — adapted to actual stack, conventions, and Brain content.

### 4. Format Guides with Section Templates

Orchestration setup creates format guides in the project's Brain docs. These teach Brain how to structure each type of memory:

```
docs/format-guides/ideas/memory.md                  → raw idea (5W)
docs/format-guides/ideas/plan/memory.md              → plan (5W + approach + decisions + risks)
docs/format-guides/ideas/work-item/
  memory.md                                          → work-item (tasks + acceptance criteria)
  1_execution_developer.md                           → developer checklist (copied to every work-item)
  2_review_reviewer.md                               → reviewer checklist (copied to every work-item)
  3_documentary_documenter.md                        → doc-writer checklist (copied to every work-item)
```

Format guides are NOT static templates. Orchestration skills teach Claude HOW to build them based on the project (stack, conventions, Makefile targets, test framework). Setup generates project-specific format guides.

Suggested tag conventions: `phase/*`, `area/*`, `subsystem/*`, `agent/*`, `priority/*`, `plan`, `work-item`, `session`.

### 5. Setup (discovery + onboarding)

Re-runnable. First run discovers project and developer needs. Re-runs evolve configuration with full project knowledge.

Setup analyzes project and recommends: which skills to activate, which agents to include, which workflows fit. Developer approves or adjusts. Configuration stored in Brain.

## Data Model

Everything lives under one kind: `ideas`. Hierarchy via parent/child sub-memories. Tags distinguish type: `plan`, `work-item`. Brain's tag-aware format guides teach Claude the right structure for each.

### Hierarchy

```
Idea (docs/ideas/<slug>/memory.md)
 ├── kind: ideas (no special tags)
 ├── raw, ad-hoc — just dump the thought
 ├── attachments (sketches, samples, reference files)
 ├── format guide: docs/format-guides/ideas/memory.md
 └── status: derived from checkboxes by Brain
      │
      └── Plan (docs/ideas/<slug>/<plan-slug>/memory.md)
           ├── kind: ideas, tags: [plan, ...inherited from idea]
           ├── sub-memory of idea (parent param)
           ├── THE most important artifact — 5W, discussions, decisions,
           │   critic findings, approaches considered, developer approval
           ├── format guide: docs/format-guides/ideas/plan/memory.md
           └── status: derived from checkboxes (rolls up to idea)
                │
                └── Work-item (docs/ideas/<slug>/<plan-slug>/<wi-slug>/)
                     ├── memory.md — the work-item (what & why)
                     │    kind: ideas, tags: [work-item, ...inherited]
                     │    sub-memory of plan (parent param)
                     ├── 1_execution_developer.md — developer agent checklist
                     ├── 2_review_reviewer.md — reviewer agent checklist
                     ├── 3_documentary_documenter.md — doc-writer agent checklist
                     ├── (attachments: screenshots, samples, etc.)
                     ├── format guide: docs/format-guides/ideas/work-item/
                     │    memory.md + all sibling .md files copied on creation
                     └── progress: memory.md checkboxes + section checkboxes
                          + child progress → rolls up to plan → idea

Decision (docs/decisions/<slug>/memory.md)
 ├── kind: decisions (separate kind — decisions outlive ideas)
 ├── refs → idea, plan, work-item (context where decision was made)
 ├── refs → other decisions, other subsystems (cross-cutting)
 ├── tags: subsystem/*, area/* (discoverable across ALL future planning)
 └── standalone — lives forever

Agent memory (docs/agent-memories/<agent-name>/memory.md)
 ├── kind: agent-memories
 ├── bullet-point learnings (appended over time)
 ├── recalled via brain.recall_agent_memory() at every spawn
 └── personal to agent role

Agent learning (docs/agent-learnings/<slug>/memory.md)
 ├── kind: agent-learnings
 ├── project-wide learnings (created via brain.create_memory)
 ├── tags: agent/*, subsystem/*
 └── discoverable by any agent during planning/execution
```

### Format Guide Resolution (Brain feature)

Brain picks the most specific format guide using tag sub-folders:

```
brain.create_memory({ kind: "ideas", tags: ["plan"] })
  → looks for docs/format-guides/ideas/plan/memory.md → FOUND → use it

brain.create_memory({ kind: "ideas", tags: ["work-item"] })
  → looks for docs/format-guides/ideas/work-item/memory.md → FOUND → use it

brain.create_memory({ kind: "ideas" })
  → no tag match → falls back to docs/format-guides/ideas/memory.md
```

First matching tag wins. Simple directory lookup.

### Sections (Brain feature)

Any .md file next to memory.md is a **section**. Brain parses them like memory.md (frontmatter, checkboxes).

**Naming convention:** `{order}_{section}_{agent}.md`
- `1_execution_developer.md` → order: 1, title: execution, agent: developer
- `2_review_reviewer.md` → order: 2, title: review, agent: reviewer
- `notes.md` → no order, no agent, still a valid section

**Frontmatter (optional, overrides filename):**
```yaml
---
title: Implementation & Migration
agent: senior-developer
order: 1
---
```

Filename = sensible defaults. Frontmatter = override when needed. Brain merges both, frontmatter wins.

**Format guides as section templates:**
```
docs/format-guides/ideas/work-item/
  memory.md                        ← format guide for memory content
  1_execution_developer.md         ← copied to every new work-item
  2_review_reviewer.md             ← copied to every new work-item
  3_documentary_documenter.md      ← copied to every new work-item
```

`brain.create_memory({ kind: "ideas", tags: ["work-item"] })` copies memory.md format + all sibling section templates. MCP response lists all created file paths.

**Progress aggregation:**
```
work-item progress =
  memory.md checkboxes
  + 1_execution_developer.md checkboxes
  + 2_review_reviewer.md checkboxes
  + 3_documentary_documenter.md checkboxes
  + child sub-memory progress (recursive)
```

**Agent routing:** Brain can filter sections by agent — "all sections assigned to developer with unchecked items" = the developer's work queue.

**Workflow ordering:** Order numbers imply sequence. Lower-numbered sections complete before higher-numbered ones start.

### Tag Propagation

Tags flow from ideas through the hierarchy:
```
Idea tags:      [area/importer, format/orcaslicer, persona/profile-creator]
     ↓ inherited + plan tag added
Plan tags:      [plan, area/importer, format/orcaslicer, persona/profile-creator]
     ↓ inherited + work-item tag added
Work-item tags: [work-item, area/importer, format/orcaslicer, phase/planned]
     ↓ relevant subset
Decision tags:  [area/importer, format/orcaslicer, subsystem/converter]
     ↓ relevant subset
Learning tags:  [area/importer, format/orcaslicer, agent/developer]
```

Searching by tag finds everything related across the hierarchy:
- `brain.browse_memories({ tags: ["plan"] })` → all plans across all ideas
- `brain.browse_memories({ tags: ["work-item"] })` → all work-items
- `brain.search_memories({ tags: ["area/importer"] })` → ideas + plans + work-items + decisions + learnings

### Lifecycle

```
Idea: living document — can be updated anytime, new requirements = new checkboxes
Plan: drafted → critic reviewed → developer approved → work-items spawned
Work-item: planned → in-progress → reviewed → done (immutable execution unit)
Decision: created during planning, lives forever, refs grow over time
```

**Ideas are living, work-items are immutable.**

Ideas evolve — developer adds new requirements, new checkboxes at any time. Nothing breaks. Old work-items stay done. New checkboxes have no work-items yet. Next planning session picks up unchecked items and creates new work-items with fresh execution/review/documentary sections.

Status is NEVER a field. Brain derives it from checkboxes automatically.
Progress rolls up: work-item checkboxes → plan aggregate → idea aggregate.

Example:
```
Idea: "Bulk import" (2/3 done)
  - [x] Parse OrcaSlicer format       → work-item (done)
  - [x] Bulk import endpoint          → work-item (done)
  - [ ] Add export too                → no work-item yet, needs planning
```

## Workflow

### 1. Capture Ideas

Developer writes ideas (file, conversation, brain UI). Each captured in Brain:
```
brain.create_memory({ title: "<idea>", kind: "ideas", tags: ["area/auth"] })
→ Brain finds format guide: docs/format-guides/ideas/memory.md
→ Response: write content to <path> using this format
→ Attach reference files if any
```

### 2. Plan (one idea at a time)

Orchestrator finds unstarted ideas:
```
brain.browse_memories({ kind: "ideas", status: "not-started" })
→ Exclude sub-memories (plans and work-items) — only top-level ideas
```

Presents list to developer. Developer picks which to plan.

For each selected idea (sequentially, finish one before starting next):
- Planning team spawns (product + critic + optional others)
- Each agent starts with `brain.recall_agent_memory()` + `brain.recall_developer_profile()`
- Orchestrator passes idea FILE PATH to planning team
- Team reads idea directly: `Read("docs/ideas/<slug>/memory.md")`
- Team reads idea attachments (samples, sketches, reference files)
- Brainstorming with ambiguity scoring if idea is vague (D020, D024)
- Consensus planning: product drafts → critic challenges → max 5 iterations (D029)
- Decisions stored in idea context AND as standalone brain memories

Create plan as sub-memory of idea:
```
brain.create_memory({
  title: "<plan name>",
  kind: "ideas",
  tags: ["plan", ...idea.tags],
  parent: "docs/ideas/<slug>/memory.md"
})
→ Brain finds format guide: docs/format-guides/ideas/plan/memory.md
→ Response: write plan content using this format
→ Plan includes: 5W, approach, decisions, risks, task breakdown, critic findings
```

Plan presented to developer (HARD GATE).

### 3. Approve

Developer reviews each plan:
- Approve → add tag: phase/approved
- Revise → agents update plan, re-present
- Reject → add tag: phase/rejected with reason
- Split → break into smaller plans
- Defer → add tag: phase/deferred

### 4. Spawn Work-items from Approved Plan

```
brain.create_memory({
  title: "<task name>",
  kind: "ideas",
  tags: ["work-item", "phase/planned", ...inherited tags],
  parent: "docs/ideas/<slug>/<plan-slug>/memory.md"
})
→ Brain finds format guide: docs/format-guides/ideas/work-item/memory.md
→ Response: write work-item content using this format
```

Brain automatically copies section templates from the format guide:
```
→ 1_execution_developer.md (from format-guides/ideas/work-item/)
→ 2_review_reviewer.md (from format-guides/ideas/work-item/)
→ 3_documentary_documenter.md (from format-guides/ideas/work-item/)
→ MCP response lists all created file paths
```

### 5. Execute

For each work-item:
- Controller passes work-item FILE PATH to execution agent
- Agent reads file directly, works through task checkboxes
- Agent checks off tasks IN the work-item file as completed
- Agent reads execution.md for quality gates
- Two-stage review: spec compliance then code quality (reviewer reads review.md)
- Agent status protocol: DONE / DONE_WITH_CONCERNS / NEEDS_CONTEXT / BLOCKED
- On completion: progress rolls up automatically (work-item → plan → idea)
- Agent learnings: quick → append to memory file, significant → brain.create_memory

### 6. Document

Doc-writer reads documentation.md checklist for the work-item.
Searches by inherited tags:
```
brain.search_memories({ query: "<what changed>", tags: [...from idea...] })
```
- Updates affected feature docs, decisions, architecture
- Screenshots new state at all relevant breakpoints (375, 768, 1280)
- Tags ensure nothing is missed

## Agent Spawning

### Planning Phase (team dialogue — warm agents)

```
Orchestrator spawns Product Agent (coordinator)
 ├── Product uses SendMessage → Critic (always)
 ├── Product uses SendMessage → UX (if frontend)
 ├── Product uses SendMessage → Tech Lead (if complex)
 └── Warm team processes ideas sequentially
     If plan grows too large → present current, spawn fresh team
```

### Execution Phase (isolation — fresh subagents)

```
Controller spawns fresh subagent per task
 ├── Receives work-item file path
 ├── Receives injected agent memories (from recall)
 ├── Clean context, no pollution from previous tasks
 ├── Two-stage review by separate subagents
 └── Learnings stored back in Brain
```

### Every Agent Spawn Includes

```
- [ ] brain.recall_agent_memory({ agent_name: "<role>" })
- [ ] brain.recall_developer_profile({})
- [ ] Inject results into agent prompt
```

## Re-runnable Setup

Setup is not one-time. Developer can trigger it again to evolve configuration:
- First run: discovery (project type, developer needs, pain points)
- Re-runs: reads current config from Brain, suggests changes based on new knowledge
- Doesn't re-ask what it knows — goes straight to "here's what I'd change and why"
- Regenerates affected agent prompts with updated project context

## Key Constraints

1. No infrastructure beyond Brain — if it needs a server, it's wrong
2. Skills are checklists with exact brain.tool() calls — not prose essays
3. Plans presented to developer before execution (configurable: manual/semi-auto/full-auto)
4. Files are read directly (Read tool) — brain.tool() only for create/search/browse/attach/recall/delete/screenshot
5. Everything is kind: ideas — tags (plan, work-item) and nesting differentiate
6. Tags propagate from ideas → plans → work-items → decisions → learnings
7. One idea planned at a time, finished before next
8. Agent memories recalled at every spawn (brain.recall_agent_memory + brain.recall_developer_profile)
9. Decisions stored standalone in kind: decisions — they outlive ideas
10. Sections (sibling .md files) use naming convention: {order}_{section}_{agent}.md
11. Format guides with section templates are teaching outputs, not static files
