---
name: cold-start
description: Build a fast, evidence-based map of an unfamiliar or stale repository before making changes. Use this skill when entering a repository for the first time, returning after a long gap, starting a task without reliable codebase context, or before editing a project whose entry points, checks, or conventions are unclear.
compatibility: Requires read access to the repository and Git history when available. This workflow is read-only.
---

# Cold Start

Learn enough about a repository to make the next decision safely without turning onboarding into a full audit.

## Preserve the working tree

Start with `git status --short` when the directory is a Git repository. Note existing changes and treat them as user-owned. Do not edit, stage, restore, or clean anything during the cold start.

## Find repository instructions

Search from the repository root for the closest applicable guidance:

- `AGENTS.md`
- `CLAUDE.md`
- `README.md`
- `CONTRIBUTING.md`
- language or tool configuration such as `.editorconfig`, linters, formatters, and test config

In monorepos, identify root guidance and package-level overrides.

## Identify entry points and commands

Inspect manifests and task files before guessing:

- JavaScript/TypeScript: `package.json`, workspace files, app routes
- Python: `pyproject.toml`, `setup.py`, package entry points
- Rust: `Cargo.toml`, binaries and workspaces
- Go: `go.mod`, `cmd/`, `main.go`
- JVM: Gradle or Maven build files
- Infrastructure: Dockerfiles, Compose, Terraform/OpenTofu, Helm, Kubernetes
- Cross-language: Makefile, justfile, task runner, CI workflows

Record how the project runs, tests, builds, and deploys. If a command is unclear, report that instead of inventing it.

## Inspect recent change patterns

When Git history exists, review roughly the last 30 days:

```bash
git log --oneline --since='30 days ago' -30
```

Summarize areas of activity, recent architectural changes, or signs that the repository is dormant. Do not list every commit.

## Map the layout

Inspect the top two or three directory levels, excluding generated and dependency directories. Explain the purpose of the important areas and identify likely owners or boundaries only when the repository documents them.

Sample a few representative implementation and test files to confirm conventions.

## Produce a compact briefing

Return no more than 10-15 lines unless the repository is unusually complex:

```markdown
## <repository> cold start

- Purpose: <what the project does>
- Entry points: <main commands or surfaces>
- Stack: <languages and frameworks>
- Layout: <key directories and roles>
- Checks: <test, typecheck, lint, build>
- Recent activity: <30-day pattern>
- Local state: <clean or existing changes>
- Conventions: <important instructions>
- Unknowns: <anything that must be verified>
- Ready for: <the requested task or next investigation>
```

This is a map for safe action, not a substitute for task-specific investigation.
