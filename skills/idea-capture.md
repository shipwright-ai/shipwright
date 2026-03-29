# Skill: Idea Capture

## Purpose

During a working session, developers often have ideas unrelated to the current task.
These ideas are valuable but acting on them immediately derails focus.
Capture them quickly, return to the current task.

## When to Trigger

Recognize idea signals during a session:
- "Oh we should also..." / "What if we..." / "Remind me to..."
- "Actually, a better approach might be..." (when unrelated to current task)
- "I just thought of something..."
- Developer starts describing a new feature while you're implementing a different one

## Checkbox Format

Every idea is a checklist. Break the idea into concrete steps so progress is
visible at a glance. Brain (and Claude) can deduce status automatically:

- **0 checked** → not started
- **some checked** → in progress
- **all checked** → done

### Example

```markdown
## Auth improvements
> Context: working on login flow, 2025-03-28

- [ ] Research refresh token best practices
- [ ] Replace long-lived session tokens
- [ ] Add token rotation on refresh
- [ ] Update API docs
```

When a step is completed, check it off:

```markdown
- [x] Research refresh token best practices
- [x] Replace long-lived session tokens
- [ ] Add token rotation on refresh
- [ ] Update API docs
```

Brain sees 2/4 → in progress. No extra status field needed.

### Sizing

- Small idea (one action) → single checkbox is fine
- Bigger idea → 3–6 checkboxes capturing the key steps
- Don't over-plan — the developer will refine when they actually work on it

## How to Capture

### If Brain is available (`brain` MCP):

1. Acknowledge briefly: "Good idea. Capturing to Brain."
2. Use `brain.create_memory`:
   - title: brief idea title
   - summary: one sentence capturing the core idea
   - kind: "ideas"
   - by: your agent name
   - tags: relevant tags (reuse existing from brain overview when possible)
   - refs: if the idea relates to an existing memory, link it
3. Brain returns the file path, format guide, and next steps — follow them all.
4. Return to current task: "Captured to Brain. Back to [current task]..."

### Updating progress in Brain

When you complete work related to a captured idea:
1. Read the idea's memory file
2. Check off the completed checkboxes
3. Save the file
Brain automatically reflects the new completion ratio in search results.

### If Brain is not available:

1. Acknowledge briefly: "Good idea. Let me capture that."
2. Append to the project's `ideas.md` file (create it if missing):
   - Use the checkbox format above
   - Each idea is an `##` heading with checkboxes beneath
3. When completing work, check off the relevant boxes in `ideas.md`.
4. Return to current task.

## Important

- Speed matters more than polish — capture in 30 seconds and move on
- Break ideas into steps, but don't over-analyze — 3–6 checkboxes is the sweet spot
- Don't let the capture process become a new conversation
- If the developer wants to discuss the idea now, gently ask:
  "Want to pause [current task] and explore this, or capture it for later?"
- Check boxes as you go — keeping status current is what makes this useful
- This is a focus tool, not a project management tool
