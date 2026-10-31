# ai-common

Private, tool-agnostic AI setup that I reuse across projects. It holds the parts that
should not be rewritten per repo: a rules-and-skills layout for Claude Code, the
guard hooks that stop an agent from doing harm, and the templates a new project starts from.

Only the owner uses it. It is private on purpose.

## What is in here

| Path | What it is | Status |
|------|-----------|--------|
| `claude-code/hooks/` | PreToolUse/PostToolUse guard hooks, copied from `dev-config` (see `docs/HOOKS.md`) | Copied; not re-tested here |
| `claude-code/settings/` | `hooks.json`, `deny.json`, `statusline.json` the installer merges into `~/.claude/settings.json` | Copied |
| `claude-code/statusline.sh` | Status line | Copied |
| `templates/CLAUDE.md` | Router-style project `CLAUDE.md` (short, with a "working on X → read Y" table) | Draft |
| `templates/settings.project.json` | Project `.claude/settings.json` with `permissions.ask` for state-changing git/gh commands (format hook still to add, lineup#158) | Draft, untested |
| `templates/rules/` | Path-scoped rules example | Example |
| `templates/context/` | On-demand context example (PR checklist) | Example |
| `skills/` | Reusable skills; start with `new-issue` | One skill, copied from `lineup` |
| `agents/` | Project agents; `convention-reviewer` is a read-only starting point | Draft |
| `.apm/` | Local APM primitives (instructions, skills, agents) | Draft, not yet compiled |
| `apm.yml` | APM manifest | Draft |
| `docs/` | How to use it, step by step | See below |

## Docs

- [Using it in a new project](docs/USING.md): the step-by-step guide.
- [Hooks](docs/HOOKS.md): what each guard does, how to change it, how to test it.
- [Claude Code hooks, in depth](docs/claude-code-hooks.md): copied from `dev-config`.
- [APM evaluation](docs/APM.md): what is decided and what is still open (#157 in `lineup`).

## Status

This is a first scaffold. Nothing here has been installed and run end to end yet. The APM
manifest has not been checked with the `apm` CLI, which is not installed on this machine.
Treat every "Draft" row above as unverified until it is run.
