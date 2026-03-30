# Execution — Execute Work-items

## When to use
Plan is approved. Work-items exist with section templates. Time to build.

## Checklist

### Find work-items to execute

- [ ] Browse approved work-items:
  ```
  brain.browse_memories({ tags: ["work-item", "phase/planned"] })
  ```
- [ ] Or read the plan directly and find child work-items
- [ ] Execute sequentially — finish one before starting next

### For each work-item

#### Capture before state (if project has UI)

- [ ] Screenshot affected pages before changes:
  ```
  brain.screenshot({ url: "<affected page>", memory_file: "<work-item memory_file>", width: 375 })
  brain.screenshot({ url: "<affected page>", memory_file: "<work-item memory_file>", width: 768 })
  brain.screenshot({ url: "<affected page>", memory_file: "<work-item memory_file>" })
  ```

#### Spawn developer subagent

- [ ] Load agent memories:
  ```
  brain.recall_agent_memory({ agent_name: "developer" })
  brain.recall_developer_profile({})
  ```
- [ ] Spawn fresh subagent with:
  ```
  Agent(prompt: """
  ## Your memories from past sessions
  <recalled memories>

  ## Your work item
  File: <work-item memory_file>
  Read it. Your tasks are the checkboxes. Check them off as you complete each.

  ## Your execution checklist
  File: <work-item dir>/1_execution_developer.md
  Follow it. Check off quality gates as you complete each.

  ## Rules
  - Read BOTH files first
  - Work through tasks in order
  - TDD: write failing test first, verify it fails, then implement
  - After each task:
    - [ ] Check off task in memory.md
    - [ ] Check off quality gates in 1_execution_developer.md
    - [ ] Run tests
  - If learned something non-obvious:
    Personal → append to <memory file from recall>
    Project-wide → brain.create_memory({ kind: "agent-learnings", tags: [...] })
  - Report: DONE / DONE_WITH_CONCERNS / NEEDS_CONTEXT / BLOCKED
  """)
  ```

#### Handle subagent status

- [ ] DONE → proceed to slop-cleaner
- [ ] DONE_WITH_CONCERNS → read concerns, address correctness/scope issues, then proceed
- [ ] NEEDS_CONTEXT → provide missing info, re-spawn with same work-item
- [ ] BLOCKED → assess root cause, escalate to developer if can't resolve

#### Slop-cleaner pass (on changed files only)

- [ ] Dead code deletion — unused imports, unreachable branches, commented-out code
- [ ] Duplication removal — copy-pasted blocks that should be consolidated
- [ ] Naming cleanup — generic names (data, result, handle)
- [ ] Test reinforcement — tests that assert too little
- [ ] Quality gates — lint, format, typecheck on changed files
- [ ] Regression test — verify nothing broke

#### Two-stage review

- [ ] Load reviewer memories:
  ```
  brain.recall_agent_memory({ agent_name: "reviewer" })
  ```
- [ ] Spawn spec compliance reviewer:
  ```
  Agent(prompt: """
  ## Your memories
  <recalled memories>

  ## Review task
  Work-item: <work-item memory_file> — read it for the spec
  Review checklist: <work-item dir>/2_review_reviewer.md — fill it in

  ## Stage 1: Spec compliance
  Does the code match what the work-item specified?
  - Missing requirements?
  - Unnecessary additions?
  - Misunderstandings?

  Report findings with severity:
  [CRITICAL] — must fix before merge
  [IMPORTANT] — should fix, may defer with reason
  [MINOR] — nice to have
  [NOTE] — observation
  """)
  ```
- [ ] If CRITICAL/IMPORTANT findings → developer fixes → re-review (max 3 cycles)
- [ ] Spawn code quality reviewer (or same reviewer, stage 2):
  ```
  Stage 2: Code quality
  - Separation of concerns
  - Error handling
  - Test coverage
  - Architecture
  - Performance
  - Security
  ```
- [ ] Record verdict in 2_review_reviewer.md: PASS / PASS WITH NOTES / FAIL
- [ ] If FAIL → developer fixes → full re-review

#### Capture after state (if project has UI)

- [ ] Screenshot affected pages after changes:
  ```
  brain.screenshot({ url: "<affected page>", memory_file: "<work-item memory_file>", width: 375 })
  brain.screenshot({ url: "<affected page>", memory_file: "<work-item memory_file>", width: 768 })
  brain.screenshot({ url: "<affected page>", memory_file: "<work-item memory_file>" })
  ```

#### Documentation

- [ ] Load doc-writer memories:
  ```
  brain.recall_agent_memory({ agent_name: "doc-writer" })
  ```
- [ ] Spawn doc-writer subagent:
  ```
  Agent(prompt: """
  ## Your memories
  <recalled memories>

  ## Documentation task
  Work-item: <work-item memory_file> — read it for what changed
  Documentation checklist: <work-item dir>/3_documentary_documenter.md — follow it

  ## Steps
  - Search for affected feature docs:
    brain.search_memories({ kind: "features", query: "<what changed>", tags: [...inherited tags...] })
  - Feature exists → update it
  - Feature doesn't exist → create it under the right parent
  - Reorganize feature tree if needed (brain.move_memory)
  - Capture feature screenshots (these are living docs, replace old ones):
    brain.screenshot({ url: "<page>", memory_file: "<feature memory_file>", width: 375 })
    brain.screenshot({ url: "<page>", memory_file: "<feature memory_file>", width: 768 })
    brain.screenshot({ url: "<page>", memory_file: "<feature memory_file>" })
  - Update architecture docs if structural changes
  - Update decision records if decisions were validated/invalidated
  - Check off items in 3_documentary_documenter.md
  """)
  ```

#### Completion

- [ ] All tasks checked off in memory.md
- [ ] All items checked off in 1_execution_developer.md
- [ ] Review verdict recorded in 2_review_reviewer.md
- [ ] Documentation complete in 3_documentary_documenter.md
- [ ] Progress rolls up automatically (work-item → plan → idea)

### Store agent learnings (all agents)

- [ ] Quality gate: non-Googleable, context-specific, actionable, hard-won
- [ ] Personal → append to memory file from recall
- [ ] Project-wide → `brain.create_memory({ kind: "agent-learnings", tags: ["agent/<role>", ...] })`

### Move to next work-item or report done

- [ ] More work-items in plan? → execute next
- [ ] All work-items done? → report to developer:
  ```
  "Plan <name> complete:
   - N work-items executed
   - All reviews: PASS
   - Docs updated: <list>
   - Learnings stored: <count>"
  ```
