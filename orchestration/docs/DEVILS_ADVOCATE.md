# Devil's Advocate — Final Review

Adversarial review of the complete design. Challenges rated by risk. All must be addressed or accepted before building.

---

## Challenge 1: Are we overengineering a simple problem?

**Concern:** 32 decisions, 6 docs, format guides with sections, tag propagation, ambiguity scoring, challenge escalation, slop-cleaner, visual-verdict, consensus planning... Is this simpler than shipwright-on-steroids? We replaced SurrealDB with 32 architectural decisions.

**Counter:** The decisions doc is a research log, not a spec. The actual system is 4 steps: capture → plan → execute → document. Most decisions describe OPTIONAL features that discovery recommends based on need. Day 1 can be just: idea capture + planning skill + execution skill.

**Resolution:** Build the core first (3 skills). Add optional skills only when tested with a real project. If a decision never gets implemented, delete it.

**Risk:** MEDIUM. Real risk of building everything before testing anything.

---

## Challenge 2: Single kind "ideas" for everything — will search get noisy?

**Concern:** Ideas, plans, and work-items all share `kind: "ideas"`. With 50 ideas each having plans and work-items, `brain.browse_memories({ kind: "ideas" })` returns 200+ items. Tags help, but what if someone forgets to filter?

**Counter:** You never browse without filters in practice. `tags: ["plan"]` or `tags: ["work-item"]` narrows immediately. Brain's search handles this. And the filesystem hierarchy is human-readable — just open the folder.

**Resolution:** Acceptable. Skills always include tag filters in their brain.tool() calls. The checklist format prevents "browse without filter" mistakes.

**Risk:** LOW.

---

## Challenge 3: Sections naming convention could be fragile

**Concern:** `1_execution_developer.md` — Brain parses order, title, and agent from the filename. What if someone names it `execution.md` (no order)? Or `1-execution-developer.md` (dashes)? Or adds a file that isn't a section?

**Counter:** Brain handles gracefully: `notes.md` → no order, no agent, still valid. Non-.md files are attachments. The convention is simple enough. Frontmatter overrides if naming doesn't fit.

**Resolution:** Brain should be lenient in parsing. Document the convention clearly. Format guide templates enforce correct naming for auto-created sections.

**Risk:** LOW.

---

## Challenge 4: Tag propagation still risks silent failures

**Concern:** If an agent creates a work-item without inheriting idea tags, search breaks silently. No error, just missing results in future planning.

**Counter:** Skill checklists include explicit `tags: [...idea.tags]`. Brain's MCP response could warn if a child memory has fewer tags than its parent.

**Resolution:** Add a self-review step in the planning skill: "verify work-item tags include idea tags." Consider Brain enhancement: warn on tag drop between parent and child.

**Risk:** HIGH. Silent failures are the worst kind. Must be addressed in skill checklists.

---

## Challenge 5: Agent memory accumulation over time

**Concern:** After 50 sessions, agent memories get noisy. Brain warns at 50+ lines but doesn't prune. Learner quality gate (D027) helps for new entries, but old entries still accumulate.

**Counter:** Setup re-run can prompt review. Developer can prune manually. The memory file is just a markdown file — easy to edit.

**Resolution:** Acceptable with mitigation. Setup re-run should flag large memory files. Learner quality gate prevents low-value entries. Over time, memory quality improves.

**Risk:** MEDIUM. Manageable but needs periodic attention.

---

## Challenge 6: Setup re-run could break customized agent prompts

**Concern:** Developer customizes `.claude/agents/product.md`. Re-running setup regenerates from archetype, potentially overwriting changes.

**Counter:** Must diff and ask, not blind overwrite.

**Resolution:** Setup must: detect existing agent files, diff against new generation, show changes, ask developer per file. Never overwrite without consent.

**Risk:** HIGH. Must be implemented correctly before re-run is safe.

---

## Challenge 7: Cost of full pipeline per idea

**Concern:** One idea through full pipeline: planning team (product + critic) + execution subagents (developer + slop-cleaner + 2 reviewers) + doc-writer = 6+ agent spawns minimum. At Claude pricing, this adds up fast.

**Counter:** Not every idea needs full pipeline. Simple fixes skip brainstorming (D024). Discovery configures which agents are active. Model tiers reduce cost (researcher → haiku, critic → sonnet, developer → opus).

**Resolution:** Skills should specify recommended model per agent role. Developer configures cost/quality trade-off. Simple work-items can skip slop-cleaner and two-stage review.

**Risk:** MEDIUM. Real concern for daily use. Model tier configuration is essential.

---

## Challenge 8: Format guide skills are "teaching files" — will Claude generate consistent output?

**Concern:** Skills teach Claude HOW to build format guides based on the project. But Claude might generate slightly different format guides each time. Inconsistency between projects or between re-runs.

**Counter:** The skill is a checklist with specific structure requirements. The output should be consistent because the inputs (project stack, Makefile, test framework) are concrete.

**Resolution:** Test with 2-3 different project types. If output varies too much, add more structure to the skill (exact sections required, example output). But don't over-constrain — project-specific adaptation is the whole point.

**Risk:** MEDIUM. Needs real-world testing.

---

## Challenge 9: Full-auto mode could produce bad results silently

**Concern:** Full-auto skips all human gates. If consensus planning (max 5 iterations) doesn't catch a bad assumption, it gets executed, reviewed by agents (who share the same blind spot), documented, and presented as done. Developer comes back to garbage.

**Counter:** Auto mode produces a log of what was auto-approved. Two-stage review catches implementation issues. Slop-cleaner catches bloat. All work is in git — revertable.

**Resolution:** Acceptable for experienced users who understand the trade-off. Default should be manual mode. Auto mode is opt-in per idea or globally via setup. The auto-mode log must be comprehensive enough that developer can spot bad decisions.

**Risk:** MEDIUM. Mitigated by opt-in nature and comprehensive logging.

---

## Challenge 10: Brain sections feature doesn't exist yet

**Concern:** D032 (sections) requires Brain to parse sibling .md files, aggregate their checkboxes, and copy them from format guides. This is new Brain functionality that hasn't been built yet.

**Counter:** The feature is well-defined and scoped. Brain already parses memory.md — extending to siblings is straightforward. Format guide copying is a small addition to create_memory.

**Resolution:** Build Brain sections feature before orchestration skills. It's a prerequisite.

**Risk:** LOW technical risk, but it's a dependency. Can't test orchestration without it.

---

## Challenge 11: Is the plan really the most important artifact?

**Concern:** We said "the plan is THE most important artifact." But if plans are verbose (5W + approach + decisions + risks + critic findings + task breakdown), they'll be long. Will developers actually read them? Or just click approve?

**Counter:** Plans should be scannable. Task list with file paths and acceptance criteria is the core. 5W and critic findings are context. The presentation format (in WORKFLOW.md) is designed for quick scanning.

**Resolution:** Keep plans focused. Task list first, context second. If a plan takes more than 2 minutes to review, it's too long — split the idea.

**Risk:** MEDIUM. Design for scannability, not comprehensiveness.

---

## Summary: Pre-build checklist

| # | Challenge | Risk | Status |
|---|-----------|------|--------|
| 1 | Overengineering | MEDIUM | Build core 3 skills first, add optional later |
| 4 | Tag propagation silent failures | HIGH | Add self-review step to planning skill |
| 6 | Setup re-run overwrites | HIGH | Implement diff+approve before re-run |
| 7 | Agent spawn costs | MEDIUM | Define model tiers per role |
| 10 | Brain sections not built | BLOCKER | Build Brain feature first |

**HIGH risks** must be addressed in implementation.
**BLOCKER** must be resolved before orchestration can be tested.
**MEDIUM risks** are accepted with mitigations noted.

## Build Order

1. Brain: sections feature (parse siblings, copy from format guides, aggregate progress)
2. Orchestration: format guide skills (teach Claude to generate project-specific format guides)
3. Orchestration: core 3 skills (ideation, planning, execution)
4. Orchestration: agent archetypes (product, critic, developer, reviewer, doc-writer)
5. Orchestration: setup (discovery + generation)
6. Test with real project (community-profiles)
7. Optional skills (brainstorming, trace, slop-cleaner, visual-verdict) — add as needed
