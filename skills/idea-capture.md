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

## How to Capture

### If Brain is available (`brain` MCP):

1. Acknowledge briefly: "Good idea. Capturing to Brain."
2. Use `brain.create_memory`:
   - title: brief idea title
   - summary: one sentence capturing the core idea
   - content: the idea in the developer's words + context of what you were working on
   - kind: "ideas"
   - by: your agent name
   - tags: relevant tags (reuse existing from brain overview when possible)
   - refs: if the idea relates to an existing memory, link it
3. If the developer pasted images or links, use `brain.attach_to_memory` to save them.
4. Return to current task: "Captured to Brain. Back to [current task]..."

### If Brain is not available:

1. Acknowledge briefly: "Good idea. Let me capture that."
2. Create a file in the ideas directory:
   - Filename: date and brief slug, e.g. `ideas/2025-03-28-auth-improvements.md`
   - Content: the idea + context
   - If assets, create a folder: `ideas/2025-03-28-auth-improvements/idea.md` + assets
3. Return to current task.

## Important

- Speed matters more than polish — capture in 30 seconds and move on
- Don't analyze or evaluate the idea — just store it
- Don't let the capture process become a new conversation
- If the developer wants to discuss the idea now, gently ask:
  "Want to pause [current task] and explore this, or capture it for later?"
- This is a focus tool, not a project management tool
