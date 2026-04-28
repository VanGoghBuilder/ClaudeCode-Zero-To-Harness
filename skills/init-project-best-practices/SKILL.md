---
name: init-project-best-practices
description: Initialize a Claude Code project using the repository's best-practice harness structure; use for new projects or existing codebases that need整理后初始化.
---

# Initialize Project with Best Practices

Use this skill when the user wants to initialize a project for Claude Code / Harness Engineering using this repository's best-practice structure.

The goal is to create a sustainable project harness, not to turn the target repository into a generic app scaffold. Prefer minimal, reversible changes and keep existing code behavior intact.

## When to use

Use this skill for requests like:
- "初始化这个项目"
- "按最佳实践初始化 Claude Code 项目"
- "把已有代码整理成最佳实践结构"
- "给这个项目加 CLAUDE.md、命令、agents、checks"
- "从空目录创建一个 Claude Code harness"

## First classify the project

Before editing, inspect the repository and classify it as one of:

1. Empty or near-empty project
   - no meaningful source code yet
   - no established docs, tests, or build commands
   - safe to create a best-practice skeleton

2. Existing codebase
   - has application/library code, build files, docs, or tests
   - must preserve current structure and behavior
   - initialize by documenting and organizing what already exists before adding harness files

Always check:
- `pwd`
- `git status --short`
- top-level files and directories
- package/build files such as `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `pom.xml`, `Makefile`
- existing docs such as `README.md`, `docs/`, `CLAUDE.md`, `.claude/`

If there are uncommitted user changes, do not overwrite them. Edit around them or ask before replacing files.

## Best-practice structure

A fully initialized project should converge on this structure when applicable:

```text
.
├── CLAUDE.md
├── README.md
├── docs/
│   ├── architecture.md
│   ├── module-boundaries.md
│   └── testing-playbook.md
├── .claude/
│   ├── settings.json
│   ├── agents/
│   │   ├── code-reviewer.md
│   │   └── test-runner.md
│   └── commands/
│       ├── implement.md
│       └── review-pr.md
└── scripts/
    └── ai/
        └── run-checks.sh
```

Only create directories and files that are useful for the project. Do not add unused infrastructure.

## Path A: empty or near-empty project

For an empty project, initialize the harness first:

1. Create `CLAUDE.md` as project memory:
   - Mission
   - Non-goals
   - Architecture or intended structure
   - Guardrails
   - Commands
   - Workflow
   - Related docs

2. Create docs:
   - `docs/architecture.md`
   - `docs/module-boundaries.md`
   - `docs/testing-playbook.md`

3. Create Claude Code configuration:
   - `.claude/settings.json`
   - `.claude/commands/implement.md`
   - `.claude/commands/review-pr.md`
   - `.claude/agents/code-reviewer.md`
   - `.claude/agents/test-runner.md`

4. Create `scripts/ai/run-checks.sh` if the project has real checks.
   - If no stack exists yet, make the script explicit about missing checks or omit it.
   - Do not invent fake `npm`/`pytest`/`go test` commands unless the stack exists.

5. Update `README.md` as the human entry point.

## Path B: existing codebase

For an existing codebase,整理后初始化:

1. Inventory the current codebase:
   - identify entry points
   - identify major modules and boundaries
   - identify test/build/lint/typecheck commands
   - identify existing docs and operational notes

2. Preserve current layout unless there is a clear, user-approved reason to move files.
   - Prefer documenting boundaries over moving source files.
   - Do not rename modules, reformat the whole tree, or introduce a new architecture as part of initialization.

3. Create or update `CLAUDE.md` based on observed facts:
   - describe the current mission and architecture
   - record real commands only
   - add guardrails that prevent common mistakes in this repo
   - link the most relevant docs

4. Add docs only where they reduce ambiguity:
   - `docs/architecture.md` for system shape and data/control flow
   - `docs/module-boundaries.md` for ownership and dependency rules
   - `docs/testing-playbook.md` for how to validate changes

5. Add `.claude/` files conservatively:
   - project commands should wrap existing workflow
   - agents should have narrow, useful responsibilities
   - settings should avoid over-broad permissions
   - ask before adding hooks that run expensive commands automatically

6. Add `scripts/ai/run-checks.sh` only after confirming real commands.
   - Use the project's actual package manager and test tools.
   - If there are multiple stacks, run checks in a stable, documented order.

## `CLAUDE.md` template

Use this shape and fill it with project-specific facts:

```markdown
# Project Memory

## Mission
- This project exists to ...
- Non-goals: ...

## Architecture
- `app/` ...
- `domain/` ...
- `infra/` ...

## Guardrails
- ...

## Commands
- test: `...`
- lint: `...`
- typecheck: `...`
- full-check: `./scripts/ai/run-checks.sh`

## Workflow
- Start by reading ...
- For cross-module changes, plan before editing.
- Before finishing, run ...

## Related Docs
- `docs/architecture.md`
- `docs/module-boundaries.md`
- `docs/testing-playbook.md`
```

## `.claude/settings.json` guidance

Keep settings minimal. A safe starting point is:

```json
{
  "permissions": {
    "deny": [
      "Read(./.env)",
      "Read(./.env.*)",
      "Read(./secrets/**)",
      "Read(./prod-data/**)"
    ],
    "ask": [
      "Bash(git push:*)",
      "Bash(rm:*)"
    ]
  }
}
```

Only add hooks when the user explicitly wants automated behavior. If adding a stop hook, make sure `scripts/ai/run-checks.sh` exists and is fast enough for routine use.

## Command templates

`implement.md` should tell Claude Code to:
- read `CLAUDE.md`
- inspect relevant docs
- identify affected modules
- make minimal changes
- run the project check command
- summarize changed files and risks

`review-pr.md` should tell Claude Code to:
- inspect branch diff and changed files
- check correctness, security, tests, and docs
- report actionable findings first
- avoid style-only noise unless it blocks maintainability

## Agent templates

Use agents only when they add clear separation:

- `code-reviewer.md`: review correctness, security, maintainability, and test coverage.
- `test-runner.md`: run the documented check command, summarize failures, and avoid editing code unless asked.

Do not create many speculative agents during initialization.

## Validation

Before finishing:

1. Run `git status --short`.
2. If files changed, inspect the diff.
3. Run available checks when they exist.
4. If checks cannot run because the project has no stack yet, say that explicitly.
5. Summarize:
   - project classification: empty project or existing codebase
   - files created/updated
   - commands discovered
   - checks run or skipped
   - follow-ups that need user decisions

## Constraints

- Do not overwrite existing `CLAUDE.md`, `.claude/settings.json`, docs, or scripts without reading them first.
- Do not move existing source files without explicit approval.
- Do not invent commands that are not supported by the repo.
- Do not add package dependencies as part of initialization unless the user asks.
- Do not commit or push unless the user explicitly asks.
