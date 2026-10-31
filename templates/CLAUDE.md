# <Project name> — Claude Code context

This file is a router. It holds only what every session needs. Detail lives in
`.claude/rules/` (loads when a matching file is touched) and `.claude/context/` (read when the
table says so).

## What this is

<One paragraph: what the project does, how it runs, what not to do.>

## Router: working on X → read Y

| Working on | Read |
|------------|------|
| Setting up a dev machine | `documentation/Setup.md` (the `setup-project` skill follows it) |
| <area> | `.claude/rules/<file>.md` |
| Workflow, branches, merge rules | `.claude/context/workflow.md` |
| Filing or editing issues | `.claude/context/issues.md` |
| Opening a PR, Dependabot | `.claude/context/pr-checklist.md` |
| Docs and where they live | `.claude/context/docs.md` |

## Always-on rules

- <Rules that apply to every change, for example "every change updates the README".>
- State-changing git or gh commands need explicit confirmation (also enforced by
  `permissions.ask` and the `ai-common` guard hooks).
- The skills listed in `apm.yml`'s lock come from `ai-common`: change them there, not in
  `.claude/skills/`, then bump the tag in `apm.yml` and run `apm install`.
