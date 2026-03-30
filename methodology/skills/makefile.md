# Skill: Makefile for Web Projects

## Why Makefiles

Makefiles are the universal interface between developers, AI agents, and the project's toolchain.
Nobody needs to remember CLI flags, docker commands, or tool-specific configurations.
`make lint` works the same whether the project uses ESLint, Ruff, or PHPCS underneath.

For AI agents specifically: a Makefile means the agent never has to figure out HOW to run
tests or lint. It just runs `make test`. This eliminates an entire class of agent errors.

## Core Pattern: FILES Variable

Every lint, format, and test target MUST support two modes:

- `make lint` — runs on the entire project
- `make lint FILES="src/foo.ts src/bar.ts"` — runs only on specified files

This is critical for agent workflows. An agent that changed 3 files should lint those 3 files,
not wait for the entire codebase to be checked. Fast inner loop, full check before commit.

The pattern:

```makefile
FILES ?=

lint:
ifdef FILES
	# lint only specified files
else
	# lint everything
endif
```

## Required Targets

Every project Makefile should have these targets. Detect what tools the project uses
and wrap them appropriately:

### `make lint`
Run the project's linter. ESLint for JS/TS, Ruff for Python, PHPCS for PHP.
If no linter is configured, tell the developer and suggest one appropriate for their stack.
Do not install one without approval.

### `make format`
Run the project's formatter. Prettier for JS/TS, Ruff format or Black for Python.
Should be safe to run anytime — formatting is non-destructive.

### `make test`
Run the test suite. Vitest/Jest for JS/TS, pytest for Python, PHPUnit for PHP.
FILES mode should run only tests related to the specified files when possible.

### `make typecheck`
Run type checking if the project uses types. tsc for TypeScript, mypy/pyright for Python.
Skip this target entirely if the project doesn't use type checking.

### `make check`
Run all quality gates in sequence: format, lint, typecheck, test.
This is the "everything must pass" target used before commits.

### `make dev`
Start the development server. Wrap whatever the project uses.
This is convenience, not a quality gate.

### `make help`
List available targets with brief descriptions.
Use this pattern:

```makefile
help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | \
		awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-20s\033[0m %s\n", $$1, $$2}'
```

Then annotate targets: `lint: ## Run linter on all files or FILES="..."`

## Stack-Specific Notes

### Next.js / SvelteKit / Node projects
- Check package.json scripts — wrap existing `npm run lint`, `npx prettier`, etc.
- For FILES with ESLint: `npx eslint $(FILES)` works directly
- For FILES with Prettier: `npx prettier --check $(FILES)` or `--write $(FILES)`
- For FILES with Vitest: `npx vitest related $(FILES) --run`
- For FILES with Jest: `npx jest --findRelatedTests $(FILES)`

### Django / Python projects
- Check for ruff, flake8, black, isort, mypy, pytest in pyproject.toml or requirements
- For FILES with ruff: `ruff check $(FILES)` works directly
- For FILES with pytest: `pytest $(FILES)` if files are test files, else use `--collect-only` to find related tests
- Wrap `python manage.py` commands where useful (migrations, shell, etc.)
- Consider a `make migrate` target

### WordPress / PHP projects
- Check for composer.json, phpcs.xml, phpunit.xml
- For FILES with PHPCS: `phpcs $(FILES)` works directly
- WordPress projects may need wp-cli targets

## When a Makefile Already Exists

Do not replace it. Read it carefully. Propose additions for missing targets.
If existing targets conflict with the patterns above (e.g. no FILES support),
propose modifications and explain why.

## When Tools Are Missing

If the project has no linter, no formatter, or no tests:
- Tell the developer what's missing
- Suggest appropriate tools for their stack
- Ask if they want to set them up
- Create Makefile targets only for tools that actually exist

Never create a `make lint` target that calls a tool that isn't installed.

## .PHONY

Mark all targets as .PHONY since they don't produce files:

```makefile
.PHONY: lint format test typecheck check dev help
```
