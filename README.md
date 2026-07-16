# Cold Start

> Map an unfamiliar repository before editing it.

## INIT PROMPT

Copy this prompt into any agentic LLM session that has filesystem access to this repository:

```text
Install the "cold-start" skill from this repository for the agent environment in which you are running.

Read this README and plugins/cold-start/skills/cold-start/SKILL.md first. Treat plugins/cold-start/skills/cold-start as the canonical skill directory. Detect whether the current environment is Claude Code, Codex, or another agent that supports directory-based skills. Prefer the environment's native plugin or marketplace installation command when available. Otherwise, copy or symlink the canonical skill directory into the documented user skill directory, such as ~/.claude/skills/cold-start for Claude Code or ~/.codex/skills/cold-start for Codex.

Before making changes, inspect existing installations and preserve user-owned files. Do not overwrite an existing skill without explicit approval. Do not install unrelated components, commit, push, publish, or edit the skill's behavior. After installation, verify that the installed SKILL.md is readable, that its frontmatter name is "cold-start", and that the agent can discover the skill. Report the exact installation method and destination path. If this environment cannot install directory-based skills, explain the exact limitation and stop.
```

## What it does

Cold Start provides a compact, evidence-based briefing on repository purpose, entry points, layout, checks, recent activity, local working state, and conventions.

## Why it exists

Agents often make early mistakes by guessing commands, missing package-level instructions, overlooking unrelated local changes, or assuming a stale architecture. A short onboarding pass prevents expensive rework.

## Acknowledgements

This skill is inspired by Boris Cherny's recommendation to begin work in an unfamiliar repository with codebase Q&A and Git-history exploration, presented in Anthropic's [Mastering Claude Code in 30 minutes](https://www.youtube.com/watch?v=6eBSHbLKuN0). The concrete checklist and implementation in this repository are independently written.

## Capabilities

- Preserves and reports existing working-tree changes.
- Finds applicable `AGENTS.md`, `CLAUDE.md`, README, and contributor guidance.
- Discovers run, test, typecheck, lint, build, and deploy commands from manifests.
- Reviews recent Git activity without dumping commit history.
- Maps important directories and monorepo boundaries.
- Returns a 10-15 line briefing with explicit unknowns.

## What it is not

- A deep architecture audit.
- Permission to edit or clean the repository.
- A replacement for task-specific investigation.
- Necessary for every single-file explanation.

## Workflow

1. Inspect local Git state.
2. Read the closest repository instructions.
3. Discover entry points and repository-native commands.
4. Summarize roughly 30 days of change patterns.
5. Map the top-level layout and sample representative files.
6. Return a concise readiness briefing.

## Safety model

The entire workflow is read-only. Existing changes are treated as user-owned and are never staged, restored, or removed.

## Repository layout

```text
.
├── .agents/plugins/marketplace.json       # Codex marketplace
├── .claude-plugin/marketplace.json        # Claude Code marketplace
├── plugins/cold-start/
│   ├── .codex-plugin/plugin.json          # Codex plugin
│   ├── .claude-plugin/plugin.json         # Claude Code plugin
│   └── skills/cold-start/
│       ├── SKILL.md                       # shared behavior
│       ├── agents/openai.yaml              # Codex UI metadata
│       ├── evals/evals.json
│       └── assets/                         # when bundled
├── AGENTS.md
├── CONTRIBUTING.md
├── SECURITY.md
├── CHANGELOG.md
└── LICENSE
```

The behavioral source of truth is [SKILL.md](plugins/cold-start/skills/cold-start/SKILL.md). Both Claude Code and Codex load this same file. Codex-specific UI metadata lives in [openai.yaml](plugins/cold-start/skills/cold-start/agents/openai.yaml).

## Bundled resources

- `evals/evals.json` - monorepo, stale Python service, and near-miss trigger scenarios.

## Local installation in Codex

From this repository:

```bash
codex plugin marketplace add .
codex plugin add cold-start@cold-start
```

To remove it later:

```bash
codex plugin remove cold-start
codex plugin marketplace remove cold-start
```

## Local installation in Claude Code

From this repository:

```bash
claude plugin marketplace add .
claude plugin install cold-start@cold-start
```

To remove it later:

```bash
claude plugin uninstall cold-start@cold-start
claude plugin marketplace remove cold-start
```

Both local marketplace flows use the same canonical `SKILL.md` and do not require a GitHub remote.

## Installation from GitHub

Codex:

```bash
codex plugin marketplace add enzovalley9/cold-start --ref main
codex plugin add cold-start@cold-start
```

Claude Code:

```bash
claude plugin marketplace add enzovalley9/cold-start
claude plugin install cold-start@cold-start
```

Public repository: <https://github.com/enzovalley9/cold-start>

## Manual skill installation

Directory-based agents can use the skill without a marketplace. From the repository root, link it into one or both supported user directories:

```bash
mkdir -p ~/.codex/skills ~/.claude/skills
ln -s "$(pwd)/plugins/cold-start/skills/cold-start" ~/.codex/skills/cold-start
ln -s "$(pwd)/plugins/cold-start/skills/cold-start" ~/.claude/skills/cold-start
```

Install only the path needed by the target agent. Use a copy instead of a symlink if the environment cannot follow links, and inspect any existing destination before replacing it.

## Evaluation

Initial review prompts live in [evals/evals.json](plugins/cold-start/skills/cold-start/evals/evals.json). They cover intended triggers, important safety boundaries, and at least one near-miss or proportionality case.

The eval prompts are included for maintainer review. They have not yet been benchmarked against a baseline, so this draft does not claim an eval score.

## Automated validation

Run the same structural checks used by CI:

```bash
bash scripts/validate.sh
```

The included GitHub Actions workflow validates the Codex and Claude Code marketplace and plugin manifests, shared skill frontmatter, Codex UI metadata, and eval structure, then runs a secret scan.

## Contributing

Before publication or a pull request:

1. Keep examples fictional and organization-neutral.
2. Validate the marketplace JSON and plugin manifest with `jq`.
3. Parse the `SKILL.md` YAML frontmatter.
4. Search for credentials, personal data, private paths, and internal identifiers.
5. Run or review the bundled eval scenarios.
6. Record behavior changes in `CHANGELOG.md`.

See [CONTRIBUTING.md](CONTRIBUTING.md) and [SECURITY.md](SECURITY.md).

## License

MIT. See [LICENSE](LICENSE).
