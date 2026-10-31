# Using ai-common

Two separate installs: the **guards** once per machine, the **workflow** once per project.

## 0. Prerequisites

- APM CLI: `curl -sSL https://aka.ms/apm-unix | sh` (Linux/macOS), then `apm --version`.
- Access to this private repo. APM uses your `gh` login (`gh auth login`), or a token in
  `GITHUB_APM_PAT`. Check with `apm auth github.com --check`.
- `bash` and `jq` on `PATH` (the guard scripts need both; on Windows, Git Bash).

## 1. Install the guards on a machine (user scope)

```bash
apm install -g LasterBergamot/ai-common/packages/guards#v0.1.0 --target claude
```

What happens:
- The scripts are copied to `~/.claude/hooks/guards/scripts/`.
- The hooks are merged into `~/.claude/settings.json` with absolute paths. Other keys in that
  file (permissions, status line, your own hooks) are kept.
- `~/.apm/apm.yml` and `~/.apm/apm.lock.yaml` record what was installed.

Then:
1. Copy `~/.claude/hooks/guards/scripts/local.sh.example` to `~/.claude/hooks/local.sh` and
   fill in this machine's protected folders and services. `local.sh` lives outside the package
   folder, so reinstalling never overwrites it.
2. **If the machine already has `dev-config`'s hooks** (`~/.claude/hooks/pretool.sh` etc.):
   remove their entries from `~/.claude/settings.json` (the ones that run
   `bash ~/.claude/hooks/<name>.sh`), otherwise every guard runs twice. Keep `local.sh`.
   `dev-config` still owns the status line, the `deny` rules and the git aliases.
3. Start a new Claude Code session. `session-start.sh` lists the active guards. Ask Claude to
   run `git push`: it should ask first, even in auto mode.

## 2. Add the workflow to a project (project scope)

From the project root:

```bash
apm init -y --target claude          # only if the project has no apm.yml yet
apm install LasterBergamot/ai-common/packages/workflow#v0.1.0
```

This adds the dependency to `apm.yml`, writes `apm.lock.yaml`, adds `apm_modules/` to
`.gitignore`, and deploys:
- the skills to `.claude/skills/<name>/SKILL.md`,
- the agent to `.claude/agents/convention-reviewer.md`.

**Commit** `apm.yml`, `apm.lock.yaml` and the deployed `.claude/skills/` and
`.claude/agents/` files. Committing them means anyone who clones the project gets the skills
without access to this private repo, and CI doesn't need a token. `apm_modules/` and
`.apm-local/` stay ignored.

If the project already has a skill with the same name (an older, project-specific copy),
delete it first; APM refuses to overwrite a file it didn't write.

## 3. Write the project's context files

The workflow skills read the project's values from `.claude/context/`. Copy the templates and
fill them in:

| File | Read by |
|------|---------|
| `templates/context/issues.md` → `.claude/context/issues.md` | `new-issue` |
| `templates/context/pr-checklist.md` → `.claude/context/pr-checklist.md` | `create-pr`, `triage-dependabot` |
| `templates/context/docs.md` → `.claude/context/docs.md` | `update-documentation` |
| `templates/context/audit.md` → `.claude/context/audit.md` | `repo-audit` |

`setup-project` needs no context file: it follows the project's setup runbook (the router's
setup row, or `documentation/Setup.md`).

For a new project, also start from `templates/CLAUDE.md` and `templates/settings.project.json`.

## 4. Check it works

- `apm audit` in the project: no drift, no hidden-Unicode findings.
- In Claude Code, `/` lists the skills. Ask for `new-issue` and check that it reads
  `.claude/context/issues.md`.

## Updating a project to a new ai-common version

```bash
# change the tag in apm.yml (e.g. #v0.1.0 -> #v0.2.0), then:
apm install
git diff .claude/   # review what changed in the deployed skills
```

Commit `apm.yml`, `apm.lock.yaml` and the changed deployed files together.
Never edit a deployed skill in the project: `apm audit` reports it as drift, and the next
install overwrites it. Change it in `ai-common` instead (see `AUTHORING.md`).
