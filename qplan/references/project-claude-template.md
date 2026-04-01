# Project CLAUDE.md Scaffolding Template

## Purpose

This template produces a project-level CLAUDE.md that supplements (not duplicates)
the global standards at `~/.claude/CLAUDE.md`. Claude Code automatically merges both.

## Principles

1. **No duplication** — Global CLAUDE.md already covers philosophy, code quality, testing
   principles, security, git conventions, and domain standards. Don't repeat them.
2. **Project-specific only** — Build/test/lint commands, architecture decisions, tech stack
   choices, and conventions unique to this project.
3. **Under 80 lines** — This is a Layer 0 file (always loaded). Keep it tight.
4. **Include commands** — Build, test, and lint commands are mandatory. Without them,
   Claude can't self-validate during implementation.

## Template

Generate a CLAUDE.md following this structure. Replace bracketed values with
project-specific details detected from context or user input.

```markdown
# [Project Name]

[One sentence: what this project does.]

## Tech Stack

[Stack] | [Framework] | [Key libraries]

## Commands

- Build: `[build command]`
- Test: `[test command]`
- Lint: `[lint command]`
- Dev: `[dev server command]`

## Architecture

[2-5 bullet points: key architectural decisions, module structure, data flow.
Only include what's decided. Leave unknowns for qplan to resolve.]

## Project Conventions

[Anything that DIFFERS from global standards. Examples:]
[- "API responses use snake_case (external contract requirement)"]
[- "All database access goes through src/db/ — no direct ORM calls elsewhere"]
[- "Feature flags in src/config/features.ts — check before new conditional logic"]

[If nothing differs from global standards, omit this section entirely.]
```

## Tech Stack Detection

When scaffolding, detect the stack from these signals (in priority order):

1. **User's task description** — "Build a FastAPI service" → Python/FastAPI
2. **Existing config files**:
   - `package.json` → Node.js (check for next, react, vue, etc.)
   - `pyproject.toml` or `setup.py` → Python (check for fastapi, django, flask, etc.)
   - `Cargo.toml` → Rust
   - `go.mod` → Go
   - `Gemfile` → Ruby
3. **Existing source files** — `.ts` files → TypeScript, `.py` files → Python, etc.
4. **If unknown** — Ask the user before scaffolding.

## Command Detection

For each detected stack, use these defaults (override if config files specify otherwise):

| Stack | Build | Test | Lint | Dev |
|---|---|---|---|---|
| Python/uv | `uv run python -m build` | `uv run pytest` | `uv run ruff check .` | `uv run uvicorn main:app --reload` |
| Python/pip | `python -m build` | `pytest` | `ruff check .` | `python -m flask run` |
| TypeScript/Next.js | `npm run build` | `npm test` | `npm run lint` | `npm run dev` |
| TypeScript/Node | `npm run build` | `npm test` | `npm run lint` | `npm start` |
| Rust | `cargo build` | `cargo test` | `cargo clippy` | `cargo run` |
| Go | `go build ./...` | `go test ./...` | `golangci-lint run` | `go run .` |

Always check actual config files (`package.json` scripts, `pyproject.toml` scripts, `Makefile`, etc.)
for project-specific commands before falling back to defaults.
