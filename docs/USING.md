# Using ai-common in a new project

Goal: a fresh repo gets the same agent guard rails and conventions in about ten minutes.

## 1. Install the machine-level hooks (once per machine)

The guard hooks run for every project, so install them from `dev-config`:

```bash
git clone git@github.com:LasterBergamot/dev-config.git
cd dev-config
bash install.sh --all        # Linux; on Windows run .\install.ps1
```

Then fill in `~/.claude/hooks/local.sh` (copied from `local.sh.example`) with the drives and
services of this machine. See `docs/HOOKS.md`.

## 2. Add the project files

1. Copy `templates/CLAUDE.md` to the project root and rewrite the router table for the project.
2. Copy `templates/settings.project.json` to `.claude/settings.json`. Keep the `ask` rules; add
   the project's allow-list (for example `task lint`, `task test`).
3. Copy the rules you need from `templates/rules/` to `.claude/rules/`. Each rule has a
   `paths:` glob, so it loads only when a matching file is touched.
4. Copy the skills you need from `skills/` to `.claude/skills/<name>/SKILL.md`.
5. Copy agents from `agents/` to `.claude/agents/` if the project needs them.

## 3. Check it works

- Start Claude Code in the project and ask it to run a state-changing command, for example
  `git push`. It should ask first, even in auto mode.
- Edit a Python or TypeScript file. `posttool-check.sh` should check its syntax. Auto-formatting is not set up yet (lineup#158).
- Run `bash claude-code/hooks/test-hooks.sh` from `ai-common` if the Windows test suite applies
  (it needs Git Bash on Windows).

## 4. Other agents (APM)

Once `apm.yml` has been validated (see `docs/APM.md`), `apm install` in the project
compiles the same instructions and skills for other tools. Until then, only Claude Code
reads these files.

## Updating a project from ai-common

ai-common is the source. Projects copy from it, so an update is a manual diff: compare the
project file with the template and take what you want. Do not symlink, because a project's
rules are tuned to that project.
