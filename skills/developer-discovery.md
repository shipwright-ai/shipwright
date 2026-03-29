# Skill: Developer Discovery

## Purpose

Understand how this specific developer works so you can recommend the right
skills, hooks, and workflows. Developers don't know what they need by name —
they know their pain points. Your job is to connect pains to solutions.

Before starting, read `../shipwright/community-insights.md` — these are real
learnings from other developers. Use them to make your recommendations concrete:
"Another developer had the same issue and found that X helped."

## When to Use

During setup, after the technical foundation is in place.
Can also be triggered anytime: "Want me to help improve your workflow?"

## The Conversation

Don't run through a checklist. Have a natural conversation. Weave these in:

### Work style
- "What does a typical coding day look like for you?"
- "What's the most annoying part of your workflow right now?"
- "Do you tend to work on one thing at a time or jump between tasks?"

### Pain points
- "What kind of bugs waste the most of your time?"
- "When you come back to code after a week, what's hardest to pick up?"
- "How often do you ship something that doesn't match what was intended?"
- "Do you ever rewrite things because you didn't know a decision had been made?"

### Team context
- "How do you get requirements — specs, conversations, rough ideas?"
- "What does code review look like?"
- "What takes new team members longest to learn?"

## Mapping Pains to Solutions

Only recommend what matches their actual pain. Not everything. Not even most things.

| Developer says...                                    | Recommend                |
|------------------------------------------------------|--------------------------|
| "I forget to run tests" / "CI catches my mistakes"   | Pre-commit hooks         |
| "I start too many things" / "I get distracted"       | Idea capture             |
| "I don't know why code is written this way"          | decisions.md             |
| "New people take forever to onboard"                 | Full docs/ knowledge base|
| "Requirements are always vague"                      | Review skill for plans   |
| "I waste time on code that gets rejected in review"  | Implement skill          |
| "My AI suggestions feel generic"                     | Better CLAUDE.md         |
| "I keep solving the same problems differently"       | conventions.md           |
| "I build the wrong thing sometimes"                  | vision.md + personas     |

## How to Recommend

Pick the top 1-2 that match their biggest pain. Use community insights to back it up:

"It sounds like [pain] is costing you time. Another developer had the same issue —
they started using [solution] and [result]. Want me to set that up?"

## Capturing What You Learn

During discovery, you'll learn things worth keeping. Scope them correctly:

- Developer's work style and preferences → `../shipwright/local/preferences.md`
- A pain/solution that would help others → ask to add to `../shipwright/community-insights.md`
- Something about this specific project → the project's `docs/`

If a developer shares a workflow improvement:
1. Ask: "This is useful — mind if I add it to Shipwright so other developers benefit?"
2. If yes, add to community-insights.md following the format there
3. Keep it anonymous
4. The developer commits and pushes to share it with the team

## Important

- Listen more than you talk
- Don't make them feel like they're doing things wrong
- Frame as "here's something that might help" not "you should do this"
- Some developers just want a Makefile and nothing else — that's a win too
- A developer who says "I'm good" is also a valid outcome
