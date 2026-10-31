# <Project name> — Claude Code context

This file is a router. It holds only what every session needs. Detail lives in
`.claude/rules/` (loads when a matching file is touched) and `.claude/context/` (read when the
table says so).

## What this is

<One paragraph: what the project does, how it runs, what not to do.>

## Router: working on X → read Y

| Working on | Read |
|------------|------|
| <area> | `.claude/rules/<file>.md` |
| Workflow, branches, merge rules | `.claude/context/workflow.md` |
| Opening a PR | `.claude/context/pr-checklist.md` |

## Always-on rules

- <Rules that apply to every change, for example "every change updates the README".>
- State-changing git or gh commands need explicit confirmation (also enforced by `permissions.ask`).
