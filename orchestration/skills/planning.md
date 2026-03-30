# Planning — Plan One Idea

## When to use
Developer selected an idea to plan. One idea at a time. Finish completely before next.

## Checklist

### Concrete signal check

- [ ] Does the idea already have specific file paths, function names, error messages, or test commands?
  - 3+ concrete signals → skip brainstorming, go straight to drafting plan
  - 0 signals → brainstorm first (see brainstorming section below)
  - 1-2 signals → ask developer: "Want to clarify further or proceed?"

### Load context

- [ ] Read the idea:
  `Read("<idea memory_file>")`
- [ ] Read any attached files referenced in the idea
- [ ] Search related decisions:
  ```
  brain.search_memories({ query: "<topic>", kind: "decisions", tags: [...idea.tags...] })
  ```
- [ ] Search past learnings:
  ```
  brain.search_memories({ query: "<topic>", kind: "agent-learnings", tags: [...idea.tags...] })
  ```
- [ ] Search related plans/work-items:
  ```
  brain.search_memories({ query: "<topic>", tags: ["plan"] })
  ```

### Spawn planning team

- [ ] Spawn Product Agent (coordinator):
  ```
  brain.recall_agent_memory({ agent_name: "product" })
  brain.recall_developer_profile({})
  ```
- [ ] Product Agent activates via SendMessage:
  - Critic (always)
  - UX (if idea involves frontend — check tags for area/frontend, area/ui)
  - Tech Lead (if architecturally complex — multiple subsystems, new infrastructure)
- [ ] Each advisor recalls their memories:
  ```
  brain.recall_agent_memory({ agent_name: "<role>" })
  ```

### Brainstorming (if needed)

- [ ] Measure ambiguity across dimensions:
  - Goal clarity (0.0-1.0) — can you state the objective in one sentence?
  - Constraint clarity (0.0-1.0) — are boundaries and limitations clear?
  - Success criteria clarity (0.0-1.0) — could you write a test that verifies success?
  - Context clarity (0.0-1.0, brownfield only) — does existing system map to codebase?
- [ ] Ask ONE question at a time, target WEAKEST dimension
- [ ] Score after every answer
- [ ] Challenge escalation:
  - Round 4+: Contrarian — "What if the opposite were true?"
  - Round 6+: Simplifier — "What's the minimal version?"
  - Round 8+: Ontologist — "What IS this, really?" (only if ambiguity > 30%)
- [ ] Don't proceed until ambiguity <= 20% (or developer says "good enough")

### Consensus planning

- [ ] Product agent drafts approach:
  - 3-5 guiding principles for this feature
  - Top 3 design drivers (what matters most)
  - 2+ viable approaches with trade-offs
  - Recommended approach with rationale
- [ ] Critic challenges via SendMessage (max 5 iterations):
  - Challenges assumptions
  - Flags overbuilding
  - Questions priority
  - Product addresses or explicitly rejects each concern
- [ ] High-risk auto-triggers deliberate mode (add pre-mortem + expanded test plan):
  - Auth/security changes
  - Database migrations
  - Destructive operations
  - Cross-subsystem changes

### Create plan

- [ ] Create plan as sub-memory of idea:
  ```
  brain.create_memory({
    title: "<plan name>",
    kind: "ideas",
    tags: ["plan", ...idea.tags],
    parent: "<idea memory_file>"
  })
  ```
  → Brain finds format guide: `docs/format-guides/ideas/plan/memory.md`
  → Write plan content:
    - **Why:** problem this solves (from idea)
    - **What:** concrete deliverables as task breakdown
    - **Who:** personas affected, agents involved
    - **When:** priority, dependencies
    - **How:** chosen approach, trade-offs considered
    - **Decisions:** made during planning
    - **Risks:** from critic, how addressed
    - **Tasks:** each becomes a work-item after approval

### Capture decisions

- [ ] For each decision made during planning:
  - Note in plan file (context)
  - Store standalone (source of truth):
    ```
    brain.create_memory({
      title: "Decision: <what was decided>",
      kind: "decisions",
      tags: ["subsystem/<x>", "area/<y>", ...relevant tags],
      refs: ["<idea memory_file>", "<plan memory_file>"]
    })
    ```

### Self-review before presenting

- [ ] No placeholders (TBD, TODO, "implement later")
- [ ] No contradictions between sections
- [ ] Each task has acceptance criteria
- [ ] All persona workflows covered
- [ ] Scope matches what developer approved during brainstorming
- [ ] Tag integrity: plan tags include ALL idea tags + "plan"

### Present plan to developer

- [ ] Format:
  ```
  ### Plan: <title>
  Idea: <link to source idea>
  Why: <one line>
  Approach: <chosen approach and why>
  Tasks:
    1. <task> — <file paths> — <acceptance criteria>
    2. <task> — <file paths> — <acceptance criteria>
    ...
  Risks (from critic):
    - <risk> — <how addressed>
  Decisions made:
    - <decision> — <rationale>
  ```

- [ ] **HARD GATE: wait for developer response**
  - "approved" / "go ahead" / "looks good" → proceed to spawn work-items
  - "change X" → revise, re-present
  - "reject" → add tag phase/rejected, note why, stop
  - "split" → break into smaller plans
  - "defer" → add tag phase/deferred, stop

### Spawn work-items from approved plan

- [ ] For each task in the approved plan:
  ```
  brain.create_memory({
    title: "<task name>",
    kind: "ideas",
    tags: ["work-item", "phase/planned", ...plan.tags],
    parent: "<plan memory_file>"
  })
  ```
  → Brain copies format guide + section templates:
    - `1_execution_developer.md`
    - `2_review_reviewer.md`
    - `3_documentary_documenter.md`
  → Write work-item content: what, why, acceptance criteria, implementation steps as checkboxes

- [ ] Update idea checkboxes to reference work-items

### Size check for warm agents

- [ ] Plan had < 5 tasks and context is manageable → keep warm team for next idea
- [ ] Plan had 5+ tasks or context is heavy → present done, spawn fresh team for remaining ideas

### Store agent learnings

- [ ] Each agent stores learnings (quality gate: non-Googleable, context-specific, actionable, hard-won):
  - Personal → append to memory file path from `recall_agent_memory`
  - Project-wide → `brain.create_memory({ kind: "agent-learnings", tags: ["agent/<role>", ...] })`
