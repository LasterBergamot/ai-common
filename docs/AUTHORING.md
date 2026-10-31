# Changing and releasing ai-common

## Where things live

- Workflow skills: `packages/workflow/.apm/skills/<name>/SKILL.md`
- Agents: `packages/workflow/.apm/agents/<name>.agent.md`
- Guard scripts: `packages/guards/scripts/`; their registration: `packages/guards/.apm/hooks/guards.json`

## Rules for a workflow skill

- **Generic procedure, project facts elsewhere.** A skill must not name a repo, a label set,
  a milestone, a board number, a command or a path that belongs to one project. Those go in
  the project's `.claude/context/*.md`, and the skill says which file to read. If a new value
  is needed, add it to the matching template in `templates/context/` too.
- Keep `model:` (and `effort:` where it matters) in the frontmatter. APM copies the file
  byte-for-byte, so Claude Code reads them as before.
- State-changing git/gh steps always ask first.

## Test a change before releasing

1. Point a scratch project at your working copy:

   ```bash
   mkdir -p /tmp/apm-try && cd /tmp/apm-try && mkdir -p .claude
   printf 'name: try\nversion: 0.0.0\ntargets: [claude]\ndependencies:\n  apm:\n    - %s\n' \
     "$HOME/Work/repos/ai-common/packages/workflow" > apm.yml
   apm install
   ```

2. For the guards, install under a throw-away home so your real settings are untouched:

   ```bash
   mkdir -p /tmp/fakehome/.claude && echo '{}' > /tmp/fakehome/.claude/settings.json
   HOME=/tmp/fakehome apm install -g "$HOME/Work/repos/ai-common/packages/guards" --target claude
   ```

   And run `bash packages/guards/scripts/test-hooks.sh`, then the same with
   `VIA_DISPATCHER=1`. Both must end with `failed: 0`, on Linux and in Git Bash.

## Release

1. Bump `version` in the changed package's `apm.yml` (SemVer: a skill that now needs a new
   context-file value is a minor bump before 1.0).
2. Commit, then tag the repo and push the tag:

   ```bash
   git tag v0.2.0 && git push origin main v0.2.0
   ```

   One tag covers both packages; the package versions say which one changed.
3. Update each project as `USING.md` describes (change the tag, `apm install`, commit).
