# ai-common

Private AI-assistant setup (Claude Code first, other tools through APM targets) that I reuse across projects and machines, distributed with
[APM](https://github.com/microsoft/apm) (Agent Package Manager). It holds what should not be
rewritten in every repo: the workflow skills, a review agent, the guard hooks that stop an
agent from doing harm, and the templates a new project starts from.

Only the owner uses it. It is private on purpose.

## Packages

| Package | Scope | What it installs | Install |
|---------|-------|------------------|---------|
| [`packages/workflow`](packages/workflow) | per project | Skills: `new-issue`, `create-pr`, `triage-dependabot`, `update-documentation`, `repo-audit`, `setup-project`. Agent: `convention-reviewer`. | In the project's `apm.yml` (see [docs/USING.md](docs/USING.md)) |
| [`packages/learning`](packages/learning) | per project, opt-in | Skills: `socratic-tutor`, `plan-lab`, `prereq-check`, `assessment-prep`, `import-matrix`, `session-log`, `review-quiz`, `roadmap-sync`. Agent: `socratic-reviewer`. Only for learning repos; no code is written for the learner. | `apm install LasterBergamot/ai-common/packages/learning#<tag>` (see [docs/USING.md](docs/USING.md)) |
| [`packages/guards`](packages/guards) | per machine (user) | Claude Code hooks: ask before commits/pushes, deletes, secret reads, network uploads and writes outside the workspace; audit log; backups; syntax check after edits. | `apm install -g LasterBergamot/ai-common/packages/guards#<tag>` |

The workflow skills are **generic**: each one reads the project's values (repo, labels,
milestones, gates, doc map) from a context file in the project: `.agents/context/issues.md`,
or `.claude/context/issues.md` in projects without `.agents/context/`. The skill holds the procedure, the project holds the facts.
Templates for those files are in [`templates/context/`](templates/context).

## Other folders

| Path | What it is |
|------|------------|
| `templates/AGENTS.md` | Router-style `AGENTS.md` for a new project, read by every AI tool |
| `templates/CLAUDE.md` | A stub `CLAUDE.md` that imports `AGENTS.md` and adds the Claude-only extras |
| `templates/settings.project.json` | Project `.claude/settings.json` with `permissions.ask` for state-changing git/gh commands |
| `templates/context/` | The context files the workflow and learning skills read |
| `templates/rules/` | An example path-scoped rule |
| `docs/` | How to use, change and release all of this |

## Docs

- [Using it](docs/USING.md): step by step, for a machine (with `dev-config`, also for
  general-purpose use without a project) and for a new or an existing project.
- [Changing and releasing](docs/AUTHORING.md): edit a skill, bump the version, tag, update projects.
- [Hooks](docs/HOOKS.md) and the [guards package README](packages/guards/README.md) (internals).
- [APM decisions](docs/APM.md): what was decided and why, and the known limits.

## Status

- `packages/workflow` is installed in `lineup` and its skills are the ones lineup uses.
- `packages/guards` is installed at user scope on the Linux dev machine, replacing
  `dev-config`'s copy of the hooks (`docs/USING.md` step 1). Its tests pass on Linux and in
  Git Bash.
- `packages/learning` is installed in `learning-lab`.
- Targets: `workflow` and `learning` deploy to `claude`, `agent-skills`, `gemini` and `codex`;
  a project picks which in its `apm.yml`. `guards` is Claude-only (`docs/APM.md`, decision 6
  and "Multiple AI tools").
