# Shipwright Orchestration — Decisions

## D001: Brain as the only infrastructure

**Decision:** No database, no app server, no custom MCP tools. Brain provides all infrastructure.

**Why:** Shipwright-on-steroids required SurrealDB, SvelteKit app, 60+ MCP tools, ONNX embeddings pipeline. The complexity of the orchestration tool exceeded the complexity of the projects it was meant to help build. Brain already handles file creation, search, screenshots, attachments, progress tracking, and agent recall.

**Trade-off:** Limited to what Brain can do. If orchestration needs something Brain can't provide, Brain needs to be extended — not orchestration.

---

## D002: Checklists with exact brain.tool() calls, not prose

**Decision:** Skills are step-by-step checklists with literal `brain.create_memory({...})` syntax, not descriptive prose.

**Why:** Discovered through iteration that Claude follows exact tool call syntax reliably but improvises badly when given vague instructions like "store this in Brain." Superpowers skills are 200-line essays that teach philosophy — effective for humans, unreliable for agents. Checklists with exact calls are deterministic.

**Trade-off:** Skills are less educational to read. A developer looking at a skill sees a recipe, not an explanation. The WHY lives in docs (this folder), not in skills.

---

## D003: Brain MCP responses as the enforcement mechanism

**Decision:** Behavioral enforcement happens through Brain's MCP tool responses (next-step checklists, reminders, forced choices), not through CLAUDE.md rules or skill prose.

**Why:** CLAUDE.md rules get compacted away as context grows. Skill prose is read once and forgotten. But Brain tool responses are injected fresh every time a tool is called. `create_memory` response says "NEXT STEPS: write content, attach files" — Claude follows it because it just arrived. The CLAUDE_REMINDER appended to every search/browse is always fresh.

**Trade-off:** Enforcement logic is split between skills (what to call) and Brain responses (what to do after). Developer must understand both layers.

---

## D004: Ideology stays, patterns evolve

**Decision:** Docs capture the WHY (human checkpoints, knowledge persistence, learning agents). Skills capture the HOW (specific tool calls, agent prompts, checklist structure). When models evolve, update skills — docs stay stable.

**Why:** Claude's capabilities change. Future models may handle prose better than checklists, or may have native memory. The workflow principles (plan before code, verify with human, update docs after) are model-independent. The implementation details are not.

**Trade-off:** Two layers to maintain. Must ensure skills stay aligned with doc principles when updating.

---

## D005: Shipwright adoption pattern — discovery, phased, re-runnable

**Decision:** Follow shipwright's onboarding model. Discovery conversation analyzes project and developer. Phased adoption — start with basics, add complexity as needed. Setup is re-runnable to evolve configuration.

**Why:** All-or-nothing adoption killed shipwright-on-steroids. Developers need different things at different stages. A solo hobby project doesn't need UX review agent. A team product does. Discovery figures this out.

**Trade-off:** More complex setup logic. Must handle partial configurations gracefully.

---

## D006: Agent archetypes in repo, project-specific prompts generated

**Decision:** Orchestration repo holds 5W agent archetypes (generic). Setup reads archetypes and generates project-specific agent prompts into developer's `.claude/agents/`. Project prompts reference actual file paths, kinds, personas, conventions.

**Why:** Generic prompts miss project context. Hardcoded prompts can't be updated. Archetypes provide the template; generation fills in project details. `git pull` on orchestration gives new archetypes; re-running setup regenerates project agents.

**Trade-off:** Generated files may drift from archetypes if developer customizes them. Need a way to mark which parts are custom vs generated.

---

## D007: Planning uses warm agents with SendMessage, execution uses fresh subagents

**Decision:** Planning phase spawns a coordinator (Product Agent) who uses SendMessage to consult Critic, UX, Tech advisors — agents stay warm across the planning session. Execution phase spawns fresh subagent per task with isolated context.

**Why:** Planning needs dialogue — critic should argue with product agent, they need shared context. Execution needs isolation — each task should start clean to prevent confusion accumulation. Superpowers proved fresh-per-task works for execution. On-steroids proved SendMessage works for planning.

**Additional:** When processing multiple ideas, warm planning agents handle them sequentially. If a plan grows too large (5+ tasks, complex), present current plans to developer and spawn fresh team for remaining ideas to prevent context overload.

**Trade-off:** Two different spawn patterns to implement and document. Developer must understand why planning and execution work differently.

---

## D008: Ideas as entry point, one at a time through planning

**Decision:** Workflow starts with idea capture (batch OK). Planning processes ideas sequentially — finish one completely (including developer approval) before starting next.

**Why:** Batch-presenting 5 half-baked plans overwhelms the developer and produces shallow plans. Sequential planning lets the team go deep on each idea, capture decisions properly, and get clean approval.

**Trade-off:** Slower for large batches. But better plans = less rework during execution.

---

## D009: Work-items reference ideas, completion flows back

**Decision:** Work-items always ref back to source idea. When work-item completes, its checkbox in the parent idea gets checked. Brain derives idea status from checkboxes automatically.

**Why:** Ideas shouldn't be orphaned from their execution. The developer should see "3/5 deliverables done" on the idea level without manually tracking.

**Trade-off:** Agents must remember to update idea checkboxes after work-item completion. Enforced via skill checklists.

---

## D010: Decisions stored standalone AND in context

**Decision:** When a decision is made during planning, record it in the idea file (context) AND as a standalone Brain memory in `decisions` kind with cross-cutting tags and refs.

**Why:** Decisions outlive ideas and work-items. "We chose JWT over sessions" matters for every future auth-related work. If the decision only lives in the idea file, it's invisible to future planning on different ideas. Standalone decisions with tags like `subsystem/auth` are discoverable across all future planning.

**Trade-off:** Some duplication. Decision appears in idea context AND as standalone memory. But the standalone one carries refs and tags for discoverability.

---

## D011: Tags propagate from ideas through the pipeline

**Decision:** When creating work-items from ideas, inherit idea tags. When creating decisions, use relevant subset of tags plus subsystem tags. When creating learnings, use relevant tags plus agent tags.

**Why:** Tags are the connective tissue for search. One tag like `area/importer` should pull in: past ideas, decisions made, agent learnings, completed work-items. This gives planning teams full context with one search.

**Trade-off:** Tag discipline required. If tags are inconsistent, search breaks down. Setup should establish tag conventions per project.

---

## D012: Agent memory — two levels

**Decision:** Quick personal learnings → append to agent's memory file (path from `recall_agent_memory`). Significant project-wide learnings → `brain.create_memory({ kind: "agent-learnings" })` with tags.

**Why:** Not everything is worth a standalone memory. "Always run migrations before tests" is personal muscle memory for the developer agent. "Profile inheritance breaks above 30 levels" is a project fact that every agent should know.

**Trade-off:** Agent must decide which level. Heuristic in skill checklist: "Is this about how I work? → personal. Is this about the project? → standalone."

---

## D013: Screenshots as first-class workflow tool

**Decision:** Product agent captures UI state before execution. UX agent reviews at multiple breakpoints (375, 768, 1280). Doc-writer captures after execution. All attached to work-items.

**Why:** Brain's `screenshot` tool with width parameter enables real visual QA. Before/after screenshots prove what changed. Multi-breakpoint captures catch responsive issues. Screenshots attached to work-items create a visual history.

**Trade-off:** Requires running frontend locally. Not applicable to backend-only work-items.

---

## D014: Idea attachments flow through planning and execution

**Decision:** Ideas can have attached files (sketches, samples, Figma exports, CSVs, competitor screenshots). Planning agents read these attachments. Execution agents can access them via idea refs.

**Why:** Planning based on actual data (sample OrcaSlicer JSON, Figma mockup) produces better plans than planning based on assumptions. Brain's `attach_to_memory` already supports any file type.

**Trade-off:** Large attachments increase disk usage. But they're just files — git handles them fine for reasonable sizes.

---

## D015: Ideas can have multiple work-items, work-items can have sub-work-items

**Decision:** One idea can spawn multiple work-items when it covers multiple aspects. Work-items can have sub-work-items using Brain's parent/child hierarchy.

Example:
```
Idea: Bootstrap new project
  ├── Work-item: Bootstrap SvelteKit
  ├── Work-item: Bootstrap Django
  └── Work-item: Bootstrap Testing
       ├── Sub: Frontend testing
       └── Sub: Backend testing
```

**Why:** Real features don't fit into single work-items. Brain's parent/child memories with progress rollup handle this natively. Sub-work-items are just `brain.create_memory({ parent: "..." })`.

**Trade-off:** Hierarchy can get deep. Keep to max 2 levels (work-item → sub-work-item) to prevent complexity.

---

## D016: Core agent roles and recommendations

**Decision:** Agent roles are tiered by recommendation strength during discovery:

**Always recommended:**
- Critic / Devil's advocate — catches wrong assumptions, challenges complexity
- Researcher — reads existing code/docs BEFORE planning, prevents "assumed X but code does Y"
- Product agent — ensures features serve personas, validates against vision

**Recommended when frontend exists:**
- UX/UI reviewer — validates workflows, breakpoints, accessibility

**Recommended for complex projects:**
- Tech lead — architecture decisions, system boundaries
- Security reviewer — auth, user input, APIs

**Always present (not optional):**
- Developer agent — writes code, reads plan as spec
- Reviewer agent — post-implementation review
- Doc-writer — closes knowledge loop, updates docs after execution

**Why:** Not every project needs every role. Solo hobby project needs critic (nobody else challenges). Team product with frontend needs product + UX. Discovery recommends based on project analysis.

**Trade-off:** Too few agents = missed issues. Too many = slow, expensive, confusing. Let developer decide after seeing recommendations.

---

## D017: Superpowers as design reference, not dependency

**Decision:** Extract superpowers' domain knowledge (brainstorming structure, TDD iron law, bite-sized tasks, two-stage review, systematic debugging). Rewrite as our checklists with brain.tool() calls. Don't depend on superpowers repo.

**Why:** Superpowers' format (200-line prose essays) is wrong for our enforcement model (checklists + Brain responses). The knowledge is excellent — Socratic dialogue, spec self-review, "3 fails = architectural." But delivery must match our system.

**Trade-off:** More upfront work writing skills. Risk of missing nuances. Mitigated by using superpowers as reference while writing.

---

## D018: Read files directly, brain.tool() only for operations

**Decision:** To read a memory, use `Read("docs/ideas/my-idea/memory.md")`. Brain tools are for: create, search, browse, attach, recall, delete, screenshot. Never use a Brain API call to read what's already a file on disk.

**Why:** Simpler, faster, no unnecessary API calls. Brain creates files and manages metadata. Claude reads files. Clean separation.

**Trade-off:** None. This is strictly simpler.

---

## D019: Work-item directories with role-specific checklists

**Decision:** A work-item is not a single file. It's a directory containing memory.md (the work-item itself) plus role-specific checklist files: execution.md, review.md, documentation.md.

**Why:** Each agent has different concerns. Developer agent needs: "all tests written, all tests pass, no deleted tests, code is solid and secure." Reviewer needs: severity-rated findings, verdict (PASS/PASS WITH NOTES/FAIL). Doc-writer needs: what to update, what to screenshot. Putting everything in one file makes it bloated and hard for agents to find their part.

**Inspiration:** Shipwright-on-steroids used this exact pattern with execution_summary.md and review.md next to plan.md. Brain supports this natively — files next to memory.md are siblings in the same directory.

**Structure:**
```
docs/work-items/<slug>/
├── memory.md          ← 5W, task checklist, refs to idea, decisions
├── execution.md       ← developer agent checklist (tests, security, quality gates)
├── review.md          ← reviewer checklist (findings with severity, verdict)
├── documentation.md   ← doc-writer checklist (what to update, screenshots)
└── (attachments)      ← before/after screenshots, samples, etc.
```

**Trade-off:** More files per work-item. But each file is focused and short — agents read only what they need.

---

## D020: Ambiguity scoring for brainstorming (inspired by deep-interview)

**Decision:** Brainstorming skill should measure ambiguity across weighted dimensions and gate output. Not just "ask Socratic questions" but score clarity after each answer and don't proceed until ambiguity is low enough.

**Dimensions:**
- Goal clarity (0.0-1.0) — can you state the objective in one sentence?
- Constraint clarity (0.0-1.0) — are boundaries and limitations clear?
- Success criteria clarity (0.0-1.0) — could you write a test that verifies success?
- Context clarity (0.0-1.0, brownfield only) — does existing system understanding map to codebase?

**Rules:**
- Ask ONE question at a time (never batch)
- Target the WEAKEST dimension
- Score after every answer
- Don't proceed until ambiguity <= 20%
- Allow early exit with clear warning

**Inspiration:** oh-my-claudecode's deep-interview skill. Adapted from prose to our checklist format.

**Why:** "Ask clarifying questions" is vague. Agents ask 1-2 obvious questions and move on. Ambiguity scoring forces them to keep digging until clarity is actually achieved. Measurable > vibes.

**Trade-off:** More rounds of Q&A before planning starts. But better plans = less rework. The 20% threshold is configurable per project via setup.

---

## D021: Reference repositories as inspiration sources

**Decision:** Explicitly reference superpowers and oh-my-claudecode as inspiration sources. Document what we took from each, what we adapted, and what we rejected.

**Repositories:**
- **superpowers** (github.com/obra/superpowers) — brainstorming with hard gates, TDD iron law, bite-sized task decomposition, subagent-driven execution with two-stage review, systematic debugging, git worktrees
- **oh-my-claudecode** (github.com/yeachan-heo/oh-my-claudecode) — deep-interview with ambiguity scoring, skill composition layers, hook-driven automation, tiered verification, Ralph persistence loops, notepad wisdom per plan

**What we adopted (adapted to our format):**
- Socratic brainstorming with hard gate (superpowers)
- Ambiguity scoring (oh-my-claudecode deep-interview)
- Bite-sized task decomposition 2-5 min (superpowers)
- Two-stage review: spec compliance + code quality (superpowers)
- TDD iron law: RED-GREEN-REFACTOR (superpowers)
- Systematic debugging 4 phases (superpowers)
- Tiered verification by risk/size (oh-my-claudecode)

**What we rejected (and why):**
- Prose essay skills (superpowers) — checklists + brain.tool() calls work better for Claude
- 848 TypeScript files + npm package (oh-my-claudecode) — we committed to "just markdown"
- Node.js hook scripts (oh-my-claudecode) — too heavy, use Claude Code native hooks if needed
- 19 agents with 4 lanes (oh-my-claudecode) — too many, discovery picks what's needed
- Magic keyword detection (oh-my-claudecode) — clever but fragile, our skills are explicit
- Skill composition layers (oh-my-claudecode) — Brain's MCP response chaining gives us implicit composition
- SQLite for state (oh-my-claudecode) — Brain handles all persistence

**Why document this:** Future contributors (and future Claude sessions) need to know what was considered and why. Prevents re-discovering and re-debating the same options.

**Trade-off:** None. Documentation is always good.

---

## D022: Trace skill for evidence-driven debugging

**Decision:** Adapt OMC's trace skill into our system. 3 parallel investigation lanes (code-path, config/env, measurement), ranked hypotheses with evidence strength, cross-checks (systems lens, premortem lens, science lens).

**Inspiration:** oh-my-claudecode trace skill + superpowers systematic debugging.

**Why:** "Look at the error and fix it" produces band-aid fixes. Tracing forces agents to gather evidence across multiple angles before forming hypotheses. The 3-lane parallel approach is faster than sequential investigation and prevents tunnel vision.

**How it works in our system:**
- Spawn 3 researcher subagents (one per lane) in parallel
- Each gathers evidence for/against hypotheses
- Controller synthesizes: ranked hypotheses, critical unknowns, discriminating probe
- Developer reviews before fix is attempted
- If fix fails 3x on same hypothesis → architectural problem, escalate

**Format:** Checklist with brain.tool() calls. Results stored as agent-learnings for future debugging in same area.

**Trade-off:** More expensive than "just fix it" for simple bugs. Concrete signal detection (D024) should gate this — only use trace for non-obvious bugs.

---

## D023: Challenge agent escalation during brainstorming

**Decision:** During brainstorming/planning, escalate the type of challenge as rounds increase:
- Round 4+: **Contrarian** — "What if the opposite were true?"
- Round 6+: **Simplifier** — "What's the minimal version that solves this?"
- Round 8+: **Ontologist** — "What IS this thing, really?" (only if ambiguity still > 30%)

**Inspiration:** oh-my-claudecode deep-interview challenge agents.

**Why:** A single critic mode produces shallow, repetitive challenges. Escalating challenge modes force deeper thinking. Contrarian catches assumptions. Simplifier catches overbuilding. Ontologist catches fundamental misunderstanding of the problem.

**Implementation:** Not separate agents — modes the critic switches to based on round count. The brainstorming skill checklist includes round tracking and mode switching.

**Trade-off:** Longer brainstorming sessions. But if you're at round 8 with 30% ambiguity, you NEED deeper questioning — shipping would be more expensive than asking.

---

## D024: Concrete signal detection to skip brainstorming

**Decision:** Before triggering brainstorming/deep-interview, check if the request is already concrete enough to skip directly to planning or execution.

**Concrete signals (skip brainstorming):**
- Specific file paths mentioned
- Function/class names referenced
- Issue/ticket numbers given
- Test commands specified
- Error messages pasted
- "Fix X in Y" pattern

**Vague signals (require brainstorming):**
- "Add feature X" without specifics
- "Improve the Y"
- "Something is wrong with Z"
- No file paths, no error messages, no concrete references

**Inspiration:** oh-my-claudecode ralplan pre-execution gate.

**Why:** Over-processing kills velocity. A developer saying "fix the typo in src/components/Nav.svelte line 42" doesn't need a Socratic interview. A developer saying "we need better search" does.

**Implementation:** A quick checklist at the start of the ideation/planning skill. Count concrete signals. >= 3 → skip to planning. 0 → full brainstorming. 1-2 → ask developer: "Want to clarify further or proceed with planning?"

**Trade-off:** Risk of skipping brainstorming when it's actually needed. But developer always has the option to say "let's brainstorm this first."

---

## D025: Slop-cleaner pass after execution, before review

**Decision:** After execution completes and before review, run a cleanup pass focused on AI-typical bloat. Five passes, bounded to changed files only:

1. **Dead code deletion** — unused imports, unreachable branches, commented-out code
2. **Duplication removal** — copy-pasted blocks that should be consolidated
3. **Naming/error cleanup** — generic names (data, result, handle), unclear error messages
4. **Test reinforcement** — tests that assert too little, missing edge cases
5. **Quality gates** — lint, format, typecheck on changed files

**Inspiration:** oh-my-claudecode ai-slop-cleaner skill.

**Why:** AI-generated code has predictable failure modes: unnecessary abstractions, verbose variable names, over-commented code, defensive checks for impossible states, and "helpful" additions nobody asked for. A focused cleanup pass on changed files catches this before the reviewer has to.

**Implementation:** A skill that runs after execution, before review template is filled. Developer agent or a fresh subagent reads changed files and applies the 5 passes. Then regression test to verify nothing broke.

**Important:** Only touch files changed in this work-item. Never "improve" surrounding code.

**Trade-off:** Extra step adds time. But reviewer sees cleaner code, review is faster, less back-and-forth.

---

## D026: Visual-verdict for screenshot comparison scoring

**Decision:** When work-items involve UI changes, capture before/after screenshots and produce a measurable comparison verdict. Score 0-100, verdict: pass (90+) / revise (70-89) / fail (<70).

**Verdict includes:**
- Category match (does it look like what was designed?)
- Differences list (specific visual discrepancies)
- Suggestions (how to fix discrepancies)
- Breakpoint coverage (mobile 375, tablet 768, desktop 1280)

**Inspiration:** oh-my-claudecode visual-verdict skill.

**Why:** "Looks ok" is not a review. A structured comparison with a score prevents subjective visual review. Product agent captures before screenshots, execution happens, then visual-verdict compares before/after at all breakpoints.

**Implementation:** Product agent uses `brain.screenshot({ url, memory_file, width })` for before state. After execution, same screenshots taken. Comparison is a checklist the reviewer fills: layout intact? Colors correct? Typography consistent? Interactive states working? Responsive behavior correct?

**Trade-off:** Only applicable to frontend work. Conditional in skills: "if project has UI." The scoring is subjective (agent-assessed, not pixel-diff), but still more rigorous than eyeballing.

---

## D027: Learner skill — disciplined memory extraction

**Decision:** At the end of significant work sessions, run a learner pass that extracts learnings with a quality gate. Not "append everything" but evaluate each potential learning against criteria:

**Quality gate — learning must be:**
- Non-Googleable (not basic framework knowledge)
- Context-specific (relevant to THIS project or THIS developer)
- Actionable (leads to concrete behavior change)
- Hard-won (discovered through struggle, not obvious)

**What passes:** "Profile inheritance in this project breaks above 30 levels — use iterative resolver instead of recursive."
**What fails:** "Always handle errors in async functions." (Googleable, generic)

**Inspiration:** oh-my-claudecode learner skill.

**Why:** Agent memories get noisy (Challenge 6 in DEVILS_ADVOCATE.md). Undisciplined appending creates 200-line memory files that pollute context. Quality gate ensures only valuable learnings persist.

**Implementation:** After execution, before session end. Agent reviews what happened, proposes learnings, self-evaluates against quality gate. Passes → append to agent memory or create standalone agent-learning. Fails → discard.

**Trade-off:** Some learnings might be filtered out incorrectly. But noisy memories are worse than missing one good learning. The quality gate can be tuned over time.

---

## D028: Deep-dive — trace + interview combined for complex problems

**Decision:** For complex, unclear problems, combine tracing (evidence gathering) with Socratic interview in a 2-stage pipeline:

1. **Trace stage:** 3 parallel investigation lanes gather codebase evidence
2. **Interview stage:** Deep-interview starts with trace results injected — questions are informed by actual evidence, not blind

**Inspiration:** oh-my-claudecode deep-dive skill.

**Why:** Asking requirements questions without understanding the codebase produces vague answers. Tracing first gives concrete evidence: "I found 3 places where auth is handled, the token is stored in localStorage, and there's no refresh mechanism. Given this, do you want to add token refresh or switch to session cookies?"

**Implementation:** Skill that chains trace → brainstorming. Trace results attached to the idea or work-item. Brainstorming skill reads trace results before asking questions. Ambiguity scoring still applies — but starting from a more informed position.

**Trade-off:** Expensive for simple features. Use concrete signal detection (D024) to gate — only deep-dive for truly unclear problems.

---

## D029: Consensus planning model (adapted from RALPLAN-DR)

**Decision:** Planning team uses a structured consensus model instead of ad-hoc dialogue:

**Short mode (default):**
- 3-5 guiding principles for this feature
- Top 3 design drivers (what matters most)
- >= 2 viable approaches with trade-offs
- Max 5 iterations of Planner → Critic challenge → revision
- Present best version to developer

**Deliberate mode (high-risk work):**
- Everything from short mode, plus:
- Pre-mortem: 3 failure scenarios and mitigations
- Expanded test plan: unit + integration + E2E + observability
- Security review if touching auth/user-input/APIs

**Auto-triggers for deliberate mode:**
- Auth/security changes
- Database migrations
- Destructive operations
- Incident response
- Cross-subsystem changes

**Inspiration:** oh-my-claudecode ralplan/RALPLAN-DR pattern.

**Why:** "Critic reviews the plan" is unstructured. Consensus model ensures: principles are stated (so everyone optimizes for the same thing), multiple approaches are considered (not just first idea), and iterations are bounded (max 5, then present best version).

**Trade-off:** More structured than needed for simple features. Concrete signal detection (D024) gates this — simple/concrete work skips consensus, goes straight to planning.

---

## D030: Everything is kind: ideas — tags and nesting differentiate

**Decision:** No separate `work-items` or `plans` kinds. Everything lives under `kind: "ideas"`. Tags distinguish type: `plan`, `work-item`. Parent/child nesting creates the hierarchy.

**Structure:**
```
docs/ideas/auth-redesign/memory.md                          → kind: ideas
docs/ideas/auth-redesign/auth-plan/memory.md                → kind: ideas, tags: [plan]
docs/ideas/auth-redesign/auth-plan/migrate-tokens/memory.md → kind: ideas, tags: [work-item]
```

**Why:** Brain already handles parent/child sub-memories, tag-based filtering, and progress rollup. Adding separate kinds would require new format guides, new browse logic, and new mental model. With one kind + tags:
- `brain.browse_memories({ tags: ["plan"] })` finds all plans
- `brain.browse_memories({ tags: ["work-item"] })` finds all work-items
- Filesystem hierarchy is human-readable
- Progress rolls up naturally through parent/child
- No Brain code changes needed

**Brain feature that enables this:** Tag-aware format guide resolution. `brain.create_memory({ kind: "ideas", tags: ["plan"] })` looks for `docs/format-guides/ideas/plan/memory.md` before falling back to `docs/format-guides/ideas/memory.md`. First matching tag wins.

**Trade-off:** All ideas, plans, and work-items share the same browse namespace. Mitigated by tag filtering — you never browse "all ideas" without a filter in practice.

---

## D031: Gate configuration — same system, different autonomy levels

**Decision:** Manual mode, semi-auto, and full-auto are the same skills with different gate configuration:

```
Manual:    all gates = HUMAN (developer approves at every step)
Semi-auto: planning gate = HUMAN, execution + review = SELF
Full-auto: all gates = SELF, hard stops only (BLOCKED, SECURITY, DESTRUCTIVE)
```

**Why:** Building separate "auto mode" and "manual mode" systems doubles the codebase. The skills are identical — only the approval mechanism changes. A gate is just: "present to developer and wait" vs "self-review and proceed."

**Implementation:** Setup stores gate preference in Brain. Skills check gate config before each checkpoint. Auto mode produces an auto-mode log in the plan showing what was auto-approved and why.

**Trade-off:** Auto mode risks bad plans executing unchecked. Mitigated by: consensus planning (max 5 iterations), two-stage review, slop-cleaner, and the auto-mode log that developer can review after.

---

## D032: Sections — sibling .md files next to memory.md

**Decision:** Any .md file next to memory.md is a section. Brain parses them (frontmatter, checkboxes) just like memory.md. Naming convention: `{order}_{section}_{agent}.md`.

**Example:**
```
docs/ideas/feature/plan/task-1/
  memory.md                        ← the work-item
  1_execution_developer.md         ← developer checklist (order 1)
  2_review_reviewer.md             ← reviewer checklist (order 2)
  3_documentary_documenter.md      ← doc-writer checklist (order 3)
```

**Naming = defaults:** `1_execution_developer.md` → order: 1, title: execution, agent: developer.
**Frontmatter = optional override:** title, agent, order fields. Frontmatter wins over filename.
**Plain files work too:** `notes.md` — no order, no agent, still a valid section.

**Format guides copy section templates automatically:**
```
docs/format-guides/ideas/work-item/
  memory.md                        ← format guide
  1_execution_developer.md         ← copied to every new work-item
  2_review_reviewer.md             ← copied to every new work-item
  3_documentary_documenter.md      ← copied to every new work-item
```

One `brain.create_memory()` call creates the full directory. MCP response lists all file paths.

**Progress aggregation:** memory.md checkboxes + all section checkboxes + child sub-memory progress = total.

**Supersedes D019** (work-item directories with role-specific checklists). Same concept, better implementation — naming convention replaces manual attachment, format guides replace setup-time copying.

**Why:** Agents need their own checklists. Putting everything in memory.md makes it bloated. Separate files with naming convention = zero config, self-documenting, Brain parses automatically.

**Trade-off:** More files per work-item. But each is focused and short — agents read only their file.

---

## D033: Ideas are living documents, work-items are immutable execution units

**Decision:** Ideas can be updated at any time — new requirements, new checkboxes, new context. Work-items are immutable snapshots of specific work. Done is done. New requirements on an idea = new work-items with fresh sections, not updates to existing work-items.

**Example:**
```
Idea: "Bulk import" (2/3 done)
  - [x] Parse OrcaSlicer format       → work-item (done)
  - [x] Bulk import endpoint          → work-item (done)
  - [ ] Add export too                → no work-item yet, needs planning
```

Developer adds "add export" to the idea. Old work-items are untouched. Next planning session sees "1 unchecked item" and creates a new work-item with fresh execution/review/documentary sections.

**Why:** No invalidation cascades. No "idea changed, pause everything." No warnings about stale work-items. The idea evolves naturally. Work-items are always valid for what they were designed to do. Progress rolls up correctly because Brain counts checkboxes — 2/3 done is accurate.

**Trade-off:** None. This is how real product work flows. New requirement = new ticket, not rewrite of existing work.

---

## D034: Features as Brain kind with hierarchy

**Decision:** Feature documentation lives as Brain memories under `kind: "features"` with parent/child hierarchy mirroring how features actually nest.

**Structure:**
```
docs/features/
  auth/memory.md                         ← "Authentication" overview
    login/memory.md                      ← "Login flow" detail
    oauth/memory.md                      ← "OAuth providers" detail
  profiles/memory.md                     ← "Profile management" overview
    import/memory.md                     ← "Profile import" detail
      orcaslicer/memory.md               ← "OrcaSlicer import" deep spec
```

**Why:** Features are searchable, taggable, linkable to decisions and ideas. Doc-writer knows where to update via `brain.search_memories({ kind: "features" })`. Feature tree grows organically as work ships — not upfront architecture.

**Format guide:** `docs/format-guides/features/memory.md` teaches Claude: what it does, how it works, who uses it (persona), screenshots, related decisions, known limitations.

**Setup discovers** documentation preferences during discovery. Default is this hierarchy. Developer can adjust.

**Doc-writer can reorganize the feature tree** — move directories, update parents, consolidate or split features as the product evolves. What starts as a top-level feature might become a sub-feature later. Brain refs update accordingly.

**Trade-off:** Another kind to manage. But features are genuinely different from ideas — they document WHAT EXISTS, not what's planned.

---

## D035: Two types of screenshots — reviewer evidence vs feature documentation

**Decision:** Screenshots serve two different purposes with different lifecycles:

**Reviewer screenshots** — attached to work-items. Evidence that work is done. Immutable. Captures state at completion. Can be recaptured by re-running review.
```
brain.screenshot({ url: "...", memory_file: "docs/ideas/.../work-item/memory.md", width: 375 })
```

**Feature screenshots** — attached to feature docs. Living documentation of current state. Deleted and recaptured whenever the feature changes.
```
brain.screenshot({ url: "...", memory_file: "docs/features/profiles/import/memory.md", width: 375 })
```

**Why:** Reviewer screenshots answer "did this work-item produce correct results?" Feature screenshots answer "how does this feature work right now?" Different questions, different lifecycles. Work-item screenshots stay forever (evidence). Feature screenshots get replaced (documentation).

**Trade-off:** More screenshots. But Brain's attach handles both identically — just different memory_file targets.
