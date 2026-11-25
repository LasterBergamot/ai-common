# <Project name>: Claude Code context

@AGENTS.md

`AGENTS.md` (imported above) is the router and rule set for every AI tool. This file adds only
what is specific to Claude Code.

## Claude-only extras

- **Settings:** `.claude/settings.json` (from `ai-common`'s `templates/settings.project.json`)
  asks before state-changing git and gh commands (`permissions.ask`) and holds any
  `permissions.deny` rules. The guard hooks from `ai-common` (user scope) add another layer.
- **Path rules:** `.claude/rules/<file>.md` loads when a matching file is touched.
- **Skills and agents:** `model:` and `effort:` in their frontmatter are honoured by Claude Code
  only.
- Sub-agents load neither this file nor the rules: put the paths they need in their prompt.
