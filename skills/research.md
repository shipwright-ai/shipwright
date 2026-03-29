# Skill: Research a Project

## Purpose

Learn about a project and build a small knowledge base in docs/.
This is used during Phase 2 of setup, or anytime the developer wants to
document their project's context for better AI-assisted development.

## Process

### 1. Explore the codebase

Read broadly, don't dive deep yet:
- Entry points (main files, index files, app configuration)
- Directory structure — what lives where
- Key configuration files (package.json, pyproject.toml, docker-compose, etc.)
- Database schema or models
- API routes or URL configuration
- Existing documentation, comments, READMEs in subdirectories

### 2. Form hypotheses

Based on what you found, draft your understanding of:
- What this project does and who it's for
- How it's architected (monolith, microservices, SSR, SPA, etc.)
- Key technical decisions visible from the code
- Patterns and conventions the team follows

### 3. Verify with the developer

Present your understanding and ask the developer to correct, confirm, or expand.
This is critical — do not write docs based on assumptions alone.

Ask specifically about:
- "Is my understanding of the architecture correct?"
- "What are the main user types / personas?"
- "Are there important decisions that aren't visible from the code?"
- "What are the biggest pain points or technical debt areas?"
- "Any external resources I should know about? API docs, design systems, other repos?"

### 4. Create docs/

Based on the verified understanding, create:

**docs/vision.md**
- What the project is (2-3 sentences)
- Who it's for (key user types, brief behavioral descriptions)
- What success looks like (key goals or metrics if known)
- One page max. This is a compass, not a novel.

**docs/architecture.md**
- System overview (how major parts connect)
- Key technology choices and why
- Data flow (how a request moves through the system)
- External dependencies (APIs, services, databases)
- Keep it to what helps someone make good implementation decisions.

**docs/decisions.md**
- Important technical and product decisions
- Format each as: Decision / Context / Why this choice / Alternatives rejected
- Include decisions the developer mentioned that aren't obvious from code
- This is the highest-value doc — it prevents re-litigating settled questions

**docs/conventions.md**
- Naming patterns (files, components, variables, API endpoints)
- Code organization (where new features go, how to structure a component)
- Error handling approach
- Testing patterns (what to test, how, naming)
- Anything that makes the codebase feel consistent

### 5. Update CLAUDE.md

Add a reference to docs/ so Claude Code loads this context in future sessions:
"For project context — vision, architecture, decisions, conventions — see docs/"

## Important

- Every doc must be verified by the developer before it's final
- Keep docs concise — they'll be read by AI agents in every session
- If you're not sure about something, ask rather than guess
- These docs will evolve — they don't need to be perfect on day one
- Don't document what's obvious from the code — document what ISN'T obvious
