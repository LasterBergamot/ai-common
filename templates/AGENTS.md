# <Project name>: AI context (any tool)

This file is the router for every AI tool (Claude Code, Gemini CLI, Codex, Copilot, Cursor, …).
It holds only what every session needs. Detail lives in `.agents/context/` (read when the table
says so) and in path-scoped rule files. `CLAUDE.md` and `GEMINI.md` only import this file.

## What this is

<One paragraph: what the project does, how it runs, what not to do.>

## Router: working on X → read Y

| Working on | Read |
|------------|------|
| Setting up a dev machine | `documentation/Setup.md` (the `setup-project` skill follows it) |
| <area> | `<path to the rule file for that area>` |
| Workflow, branches, merge rules | `.agents/context/workflow.md` |
| Filing or editing issues | `.agents/context/issues.md` |
| Opening a PR, Dependabot | `.agents/context/pr-checklist.md` |
| Docs and where they live | `.agents/context/docs.md` |

## Always-on rules

- <Rules that apply to every change, for example "every change updates the README".>
- State-changing git or gh commands need explicit confirmation.
- The skills and agents come from `ai-common` via APM (`apm.yml`, `apm.lock.yaml`) and are
  deployed per tool (`.claude/skills/`, `.agents/skills/`, …). Never edit the deployed copies:
  change this project's values in `.agents/context/`, or change the skill in `ai-common`, bump
  the tag in `apm.yml` and run `apm install`.
