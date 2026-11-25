# Changing and releasing ai-common

## Where things live

- Workflow skills: `packages/workflow/.apm/skills/<name>/SKILL.md`
- Learning skills: `packages/learning/.apm/skills/<name>/SKILL.md`; its agent: `packages/learning/.apm/agents/socratic-reviewer.agent.md`
- Agents: `packages/workflow/.apm/agents/<name>.agent.md`
- Guard scripts: `packages/guards/scripts/`; their registration: `packages/guards/.apm/hooks/guards.json`

## Rules for a workflow skill

- **Generic procedure, project facts elsewhere.** A skill must not name a repo, a label set,
  a milestone, a board number, a command or a path that belongs to one project. Those go in
  the project's context folder, and the skill says which file to read. Refer to it as
  `<context>/<file>.md` and keep the one-line definition at the top of the skill
  (`.agents/context/`, falling back to `.claude/context/`). If a new value
  is needed, add it to the matching template in `templates/context/` too.
- Keep `model:` (and `effort:` where it matters) in the frontmatter. APM copies the file
  byte-for-byte, so Claude Code reads them as before.
- State-changing git/gh steps always ask first.
- **Neutral wording, any tool.** The body must work in any AI tool: say "read the file",
  "search the web", "run", "if the tool supports sub-agents". Never name a Claude tool
  (`Read`, `Grep`, `Bash`, `WebSearch`, `Agent`) in the text. Tool-specific keys (`model:`,
  `effort:`, `tools:`) belong in the frontmatter only; other tools ignore them
  (`docs/APM.md`, "Multiple AI tools").
- Router files: name `AGENTS.md` first, `CLAUDE.md` as the fallback.

## Extra rules for the learning package

- **Every skill and every agent declares both `model:` and `effort:`.** Here they are not
  optional: tutoring quality depends on them, and the package README lists them. Change the
  README table when you change a value.
- Claude Code honours `effort:` in skill frontmatter. For agents, `effort:` is declared too,
  but if a Claude Code version ignores it there, the agent runs at the session's effort.
  `model:` still applies. Check the Claude Code subagent docs when you upgrade.
- **No-code rule:** every primitive says that it never writes lab code, Dockerfiles, compose
  files, manifests, IaC, scripts or configs, and writes Markdown only. A change must not weaken
  that.
- Project facts come from `<context>/learning.md`. A new value goes into
  `templates/context/learning.md` too, under one of its existing headings. Projects copy those
  headings, so don't rename them.

## Test a change before releasing

1. Point a scratch project at your working copy:

   ```bash
   mkdir -p /tmp/apm-try && cd /tmp/apm-try && mkdir -p .claude
   printf 'name: try\nversion: 0.0.0\ntargets: [claude, agent-skills]\ndependencies:\n  apm:\n    - %s\n    - %s\n' \
     "$HOME/Work/repos/personal/ai-common/packages/workflow" \
     "$HOME/Work/repos/personal/ai-common/packages/learning" > apm.yml
   apm install && apm audit
   ```

   Check both `.claude/skills/` and `.agents/skills/`, and read APM's "lossy" warnings: they
   name frontmatter another tool drops. A package deploys only to the targets in its own
   `apm.yml`, so a new target goes there first.

2. For the guards, install under a throw-away home so your real settings are untouched:

   ```bash
   mkdir -p /tmp/fakehome/.claude && echo '{}' > /tmp/fakehome/.claude/settings.json
   HOME=/tmp/fakehome apm install -g "$HOME/Work/repos/personal/ai-common/packages/guards" --target claude
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
