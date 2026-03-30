# Shipwright — Setup

Progressive onboarding. Each layer builds on the previous. Developer chooses how far to go.

## Pre-checks

- [ ] Determine setup state:
  - No CLAUDE.md or no shipwright references → fresh setup, start at Layer 1
  - Has CLAUDE.md with shipwright methodology → Layer 1 done, offer Layer 2
  - Has .mcp.json with brain → Layer 2 done, offer Layer 3
  - Has .claude/agents/shipwright-* → Layer 3 done, this is a re-run (go to Re-run section)

## Layer 1: Methodology (always)

Best practices for any project. No dependencies.

### Project analysis

- [ ] Read project files to understand the stack:
  - `Read("CLAUDE.md")` — existing instructions
  - `Read("Makefile")` or `Read("package.json")` or `Read("pyproject.toml")` — build system
  - `Read("docker-compose.yml")` — infrastructure
  - Check for frontend: `Glob("src/**/*.svelte")`, `Glob("src/**/*.tsx")`, `Glob("src/**/*.vue")`
  - Check for tests: `Glob("**/test*/**")`, `Glob("**/*test*.*")`, `Glob("**/*spec*.*")`
  - Check for existing docs: `Glob("docs/**/*.md")`

- [ ] Determine project characteristics:
  - Type: greenfield / brownfield
  - Stack: backend only / frontend only / fullstack
  - Framework(s), test framework(s), build system
  - Has UI / API / auth / Docker

### Set up methodology

- [ ] Create or improve Makefile with standard targets:
  - `make test`, `make lint`, `make format`, `make dev`, `make build`
  - Adapted to actual stack (pytest, vitest, ruff, eslint, etc.)

- [ ] Create or improve CLAUDE.md:
  - Project identity and stack
  - Workflow rules (capture before code, refine with developer, implement after approval)
  - Quality gates (lint, test, format before commit)
  - Context gates (load docs on demand, keep CLAUDE.md lean)

- [ ] Developer discovery:
  - "How do you prefer to work? (terse/detailed, review everything/trust agents)"
  - "What burned you before?"
  - Store preferences

### Offer next layer

- [ ] "Want persistent memory with Brain? Ideas, decisions, learnings — all searchable across sessions. [yes/no/later]"
  - yes → continue to Layer 2
  - no/later → done, summarize what was set up

---

## Layer 2: Brain (optional, recommended)

Persistent memory via MCP. Ideas, decisions, learnings — searchable, tracked, with screenshots.

### Install Brain

- [ ] Add Brain MCP to project's `.mcp.json`:
  ```json
  {
    "mcpServers": {
      "brain": {
        "command": "npx",
        "args": ["github:xxx/shipwright-brain", "mcp", "--dir", "./docs"]
      }
    }
  }
  ```
  - Ask developer to confirm the path (default: `./docs`)

### Set up docs structure

- [ ] Create `docs/` directory if it doesn't exist
- [ ] Create `docs/README.md`:
  ```
  # Project Knowledge Base

  This directory is managed by Shipwright Brain.

  ## Browse

  Run `make brain-ui` then open http://localhost:3111

  ## Structure

  - ideas/ — captured ideas, plans, work-items
  - decisions/ — architectural and product decisions
  - features/ — living feature documentation
  - agent-memories/ — agent learnings across sessions
  - format-guides/ — templates for Brain memory types
  ```

### Add Makefile target

- [ ] Add to Makefile:
  ```makefile
  brain-ui:
  	npx github:xxx/shipwright-brain ui --dir ./docs
  ```

### Update CLAUDE.md

- [ ] Add Brain references to CLAUDE.md:
  ```
  ## Brain (persistent memory)

  This project uses Shipwright Brain for persistent memory.
  - Ideas, decisions, learnings stored in docs/
  - Search: brain.search_memories({ query: "...", kind: "..." })
  - Recall: brain.recall_agent_memory({ agent_name: "..." })
  - Browse: brain.browse_memories({ kind: "...", tags: [...] })
  - Read files directly: Read("docs/ideas/.../memory.md")
  ```

### Offer next layer

- [ ] "Want structured planning with agent teams? Capture ideas → plan with critic → execute with review → docs stay current. [yes/no/later]"
  - yes → continue to Layer 3
  - no/later → done, summarize what was set up

---

## Layer 3: Orchestration (optional, needs Brain)

Structured workflow with agent teams. Ideas → Plans → Work-items → Execute → Document.

### Developer interview

- [ ] Ask:
  - "How do you want to work? Review everything (manual), review plans only (semi-auto), or full autopilot?"
  - "Do you work solo or with a team?"
  - "Any existing documentation conventions?"

### Recommend agents

- [ ] Present recommendations based on project analysis:
  ```
  Based on what I see, here's my recommended team:

  **Core (strongly recommended):**
  - Critic — [reason]
  - Product — [reason]
  - Doc-writer — [reason]

  **Project-specific:**
  - UX/UI — [only if frontend, reason]
  - Tech Lead — [only if complex, reason]
  - Security — [only if auth/API, reason]

  **Always present:**
  - Developer — executes work-items
  - Reviewer — two-stage code review

  Want to adjust?
  ```
- [ ] Developer approves or adjusts

### Generate format guides

Read the orchestration teaching files and generate project-specific format guides:

- [ ] Read orchestration skills to understand format guide requirements:
  - `Read("<shipwright>/orchestration/skills/ideation.md")`
  - `Read("<shipwright>/orchestration/skills/planning.md")`
  - `Read("<shipwright>/orchestration/skills/execution.md")`

- [ ] Generate `docs/format-guides/ideas/memory.md`:
  5W structure, 3-6 checkboxes, tag conventions for this project

- [ ] Generate `docs/format-guides/ideas/plan/memory.md`:
  5W, approach, decisions, risks, task breakdown, critic findings

- [ ] Generate `docs/format-guides/ideas/work-item/memory.md`:
  What, why, implementation steps as checkboxes, acceptance criteria

- [ ] Generate `docs/format-guides/ideas/work-item/1_execution_developer.md`:
  Project-specific quality gates based on stack:
  - Always: implementation complete, no unrelated changes, follows patterns
  - If tests: test commands from actual Makefile (`make test`, `pytest`, etc.)
  - If linting: actual lint/format commands
  - If Docker: container health checks
  - If frontend: responsive, accessible, no console errors
  - If API: contract matches docs, consistent errors
  - If auth: auth checks, no secrets in code

- [ ] Generate `docs/format-guides/ideas/work-item/2_review_reviewer.md`:
  Spec compliance + code quality checklist, severity levels, verdict

- [ ] Generate `docs/format-guides/ideas/work-item/3_documentary_documenter.md`:
  Feature doc update checklist, screenshot capture, architecture review

- [ ] Generate `docs/format-guides/features/memory.md`:
  Feature documentation structure for this project

### Generate agent prompts

- [ ] For each approved agent role:
  - Read archetype: `Read("<shipwright>/orchestration/agents/<role>.md")`
  - Generate project-specific prompt into `.claude/agents/shipwright-<role>.md`:
    - Keep 5W structure and operations checklists
    - Replace generic references with project-specific:
      - Actual file paths and conventions
      - Actual test/lint/build commands
      - Actual persona names and subsystem names
    - Add `<!-- generated by shipwright setup -->` header

### Generate project skills

- [ ] Generate `.claude/skills/capture/SKILL.md`:
  ```
  ---
  name: capture
  description: Capture ideas into Brain
  ---

  Read the ideation teaching file and follow it:
  Read <shipwright>/orchestration/skills/ideation.md

  Use Brain docs at: ./docs
  ```

- [ ] Generate `.claude/skills/plan/SKILL.md`:
  ```
  ---
  name: plan
  description: Plan an idea with agent team review
  ---

  Read the planning teaching file and follow it:
  Read <shipwright>/orchestration/skills/planning.md

  Use Brain docs at: ./docs
  Active agents: <list of approved agents>
  Gate mode: <manual/semi-auto/full-auto>
  ```

- [ ] Generate `.claude/skills/execute/SKILL.md`:
  ```
  ---
  name: execute
  description: Execute approved work-items
  ---

  Read the execution teaching file and follow it:
  Read <shipwright>/orchestration/skills/execution.md

  Use Brain docs at: ./docs
  Active agents: <list of approved agents>
  ```

### Update CLAUDE.md

- [ ] Add orchestration references:
  ```
  ## Orchestration

  Workflow: /capture → /plan → /execute
  Gate mode: <manual/semi-auto/full-auto>
  Active agents: <list>

  Agents are in .claude/agents/shipwright-*
  Format guides are in docs/format-guides/
  ```

### Store configuration

- [ ] Store setup config in Brain:
  ```
  brain.create_memory({
    title: "Decision: Shipwright setup",
    kind: "decisions",
    tags: ["orchestration", "setup"]
  })
  ```
  → Write: layers active, gate mode, active agents, project characteristics, date

### Summary

- [ ] Present to developer:
  ```
  Shipwright is set up:

  Layer 1: Methodology ✓
    - CLAUDE.md, Makefile, quality gates

  Layer 2: Brain ✓
    - Persistent memory in docs/
    - Browse: make brain-ui → http://localhost:3111

  Layer 3: Orchestration ✓
    - Gate mode: <mode>
    - Agents: <list>
    - Skills: /capture, /plan, /execute

  To start: /capture your ideas
  To reconfigure: /shipwright:setup
  ```

---

## Re-run (evolve configuration)

When setup detects previous configuration:

- [ ] Read existing config:
  ```
  brain.search_memories({ query: "shipwright setup", kind: "decisions", tags: ["setup"] })
  ```
- [ ] Read current state — don't re-ask what's known
- [ ] Ask developer: "What do you want to change?"

### If adding a new layer

- [ ] Jump to that layer's section above

### If changing agents

- [ ] For NEW agents → generate prompt into `.claude/agents/shipwright-<role>.md`
- [ ] For REMOVED agents → ask developer to confirm deletion
- [ ] For EXISTING agents with updates:
  - Diff current file against what would be generated
  - Show changes
  - Apply only approved changes
  - Preserve content NOT under `<!-- generated by shipwright setup -->` headers

### If changing gate mode

- [ ] Update CLAUDE.md
- [ ] Update project skills (they reference gate mode)

### If updating format guides

- [ ] Show diff
- [ ] Apply approved changes
- [ ] Note: existing work-items keep their old sections — changes affect new work-items only

### Store updated config

- [ ] Update the setup decision in Brain with new settings and date
