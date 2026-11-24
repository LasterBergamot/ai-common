# ai-common

Private Claude Code setup that I reuse across projects and machines, distributed with
[APM](https://github.com/microsoft/apm) (Agent Package Manager). It holds what should not be
rewritten in every repo: the workflow skills, a review agent, the guard hooks that stop an
agent from doing harm, and the templates a new project starts from.

Only the owner uses it. It is private on purpose.

## Packages

| Package | Scope | What it installs | Install |
|---------|-------|------------------|---------|
| [`packages/workflow`](packages/workflow) | per project | Skills: `new-issue`, `create-pr`, `triage-dependabot`, `update-documentation`, `repo-audit`, `setup-project`. Agent: `convention-reviewer`. | In the project's `apm.yml` (see [docs/USING.md](docs/USING.md)) |
| [`packages/learning`](packages/learning) | per project, opt-in | Skills: `socratic-tutor`, `plan-lab`, `assessment-prep`, `import-matrix`, `session-log`, `review-quiz`, `roadmap-sync`. Agent: `socratic-reviewer`. Only for learning repos; no code is written for the learner. | `apm install LasterBergamot/ai-common/packages/learning#<tag>` (see [docs/USING.md](docs/USING.md)) |
| [`packages/guards`](packages/guards) | per machine (user) | Claude Code hooks: ask before commits/pushes, deletes, secret reads, network uploads and writes outside the workspace; audit log; backups; syntax check after edits. | `apm install -g LasterBergamot/ai-common/packages/guards#<tag>` |

The workflow skills are **generic**: each one reads the project's values (repo, labels,
milestones, gates, doc map) from a context file in the project, for example
`.claude/context/issues.md`. The skill holds the procedure, the project holds the facts.
Templates for those files are in [`templates/context/`](templates/context).

## Other folders

| Path | What it is |
|------|------------|
| `templates/CLAUDE.md` | Router-style `CLAUDE.md` for a new project |
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
- Claude Code is the only target, by decision (`docs/APM.md`, decision 6).
