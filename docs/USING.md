# Using ai-common

Two separate installs: the **guards** once per machine, the **workflow** once per project.
A learning repo can also add the opt-in **learning** package (see "Add the learning package").

## 0. Prerequisites

- APM CLI: `curl -sSL https://aka.ms/apm-unix | sh` (Linux/macOS), then `apm --version`.
- Read access to this repo. It is public, so no token is needed for it. If there is one, APM
  uses a token in `GITHUB_APM_PAT`, or your `gh` login if it can read it. Check with
  `apm auth github.com --check`. If it reports "no credential found" although `gh auth status`
  is fine (the token is in the system keyring), pass the `gh` token for the one command:
  `GITHUB_APM_PAT=$(gh auth token) apm install …`. A failed auth shows up as "all probes
  failed", which looks like a wrong path or tag.
- `bash` and `jq` on `PATH` (the guard scripts need both; on Windows, Git Bash).

## 1. Install the guards on a machine (user scope)

The guards don't depend on any project: they protect every Claude Code session on the
machine, so install them on a machine you only use Claude on for general work too. That
needs only step 0 and this step; the workflow (step 2 onwards) is for projects. For the rest
of the machine setup (status line, deny rules, git aliases), see
[Machine setup with dev-config](#machine-setup-with-dev-config) below.

```bash
apm install -g LasterBergamot/ai-common/packages/guards#v0.1.1 --target claude
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
2. **If the machine has `dev-config`'s old copy of the hooks** (`~/.claude/hooks/pretool.sh`
   etc.), every guard runs twice. Remove it with dev-config's installer:
   `bash install.sh --only claude-old-hooks` (see below). It keeps `local.sh`.
3. Start a new Claude Code session. `session-start.sh` lists the active guards. Ask Claude to
   run `git push`: it should ask first, even in auto mode.

## Machine setup with dev-config

Two of my repos set up a machine for Claude Code, and they don't overlap:

| Repo | Installs | How |
|------|----------|-----|
| `dev-config` | Status line, `permissions.deny` rules for disk-wiping commands, the Antigravity CLI status line, git aliases | Its own installer, `bash install.sh` (`.\install.ps1` on Windows) |
| `ai-common` | The guard hooks (`packages/guards`) | `apm install -g` (step 1) |
| `ai-common` | Workflow skills and the review agent (`packages/workflow`) | Per project, only where you build something (step 2) |

Both write to `~/.claude/settings.json`, each only its own keys: dev-config sets `statusLine`
and adds to `permissions.deny`; APM adds hook entries whose commands run
`~/.claude/hooks/guards/scripts/`. Either can run first, and re-running either keeps the
other's entries.

### A general-purpose machine

For Claude Code without any project (questions, writing, scripts, admin work):

1. Prerequisites: `bash`, `jq`, `git`, `gh` logged in, the APM CLI (step 0). On Windows, Git for
   Windows provides `bash`.
2. Clone `dev-config` and run its installer. Pick `claude-statusline`,
   `claude-settings-statusline`, `claude-settings-deny` and, if you want them, `git-aliases`.
3. Install the guards (step 1):
   `GITHUB_APM_PAT=$(gh auth token) apm install -g LasterBergamot/ai-common/packages/guards#<tag> --target claude`.
4. Create `~/.claude/hooks/local.sh` from `~/.claude/hooks/guards/scripts/local.sh.example`.
   List this machine's folders that must never be written without asking (media libraries,
   backup drives) and its services. Add a matching `Edit(//d/Media/**)`-style rule to
   `permissions.deny` by hand: deny rules still hold when the hooks don't run.
5. Start a new session. The session-start message lists the active guards, and the status line
   shows. Ask Claude to run `git push` in any repo: it must ask first.

Skip `packages/workflow` and the project steps: they only matter inside a project repo. The
guards work the same with or without a project. `guard-workspace.sh` treats Claude's working
directory as the workspace, git repo or not, and asks before any write elsewhere (temp and
`~/.claude` excepted). So start Claude in the folder you want it to write to, for example a
`~/scratch` folder, not your home folder.

### Moving a machine off dev-config's old hooks

dev-config used to copy the hooks straight into `~/.claude/hooks/`. On such a machine:

1. Update `dev-config` (`git pull`) and run `bash install.sh --only claude-old-hooks`. It removes
   the old entries from `settings.json` first and then moves the scripts to
   `~/.claude/dev-config-backups/<timestamp>/old-hooks/`. That order matters: `pretool.sh` is
   registered with `onFailure: "block"`, so a registered but missing script would block every
   tool call. `local.sh` stays.
2. Install the guards from here (step 1).

### Updating

- dev-config: `git pull`, then `bash install.sh`. The menu preselects what changed.
- Guards: install the new tag with `apm install -g …#<new tag> --target claude`. The scripts
  are replaced and `local.sh` is kept.

## 2. Add the workflow to a project (project scope)

From the project root:

```bash
apm init -y --target claude          # only if the project has no apm.yml yet
apm install LasterBergamot/ai-common/packages/workflow#v0.2.0
```

This adds the dependency to `apm.yml`, writes `apm.lock.yaml`, adds `apm_modules/` to
`.gitignore`, and deploys:
- the skills to `.claude/skills/<name>/SKILL.md`,
- the agent to `.claude/agents/convention-reviewer.md`.

**Commit** `apm.yml`, `apm.lock.yaml` and the deployed `.claude/skills/` and
`.claude/agents/` files. Committing them means anyone who clones the project gets the skills
without installing APM, and CI doesn't need to fetch this repo. `apm_modules/` and
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
| `templates/context/learning.md` → `.claude/context/learning.md` | every `learning` skill and the `socratic-reviewer` agent (only if the learning package is installed) |

`setup-project` needs no context file: it follows the project's setup runbook (the router's
setup row, or `documentation/Setup.md`).

For a new project, also start from `templates/CLAUDE.md` and `templates/settings.project.json`.

## Add the learning package (opt-in, project scope)

For a learning repo only. The skills tutor Socratically and never write code for the learner
(`packages/learning/README.md`). APM installs only what the project's own `apm.yml` lists, so
projects that list only `workflow` (lineup, for example) never get these skills.

```bash
apm install LasterBergamot/ai-common/packages/workflow#<tag>   # the learning skills draft issues with new-issue
apm install LasterBergamot/ai-common/packages/learning#<tag>
```

The skills deploy to `.claude/skills/` and the `socratic-reviewer` agent to `.claude/agents/`.
Commit them as in step 2. Then copy `templates/context/learning.md` to
`.claude/context/learning.md` and fill it in: paths, tutor tone, tracks, platforms, formats,
the Status values and the visualization markers. The package supplies only the skills. Enforce
the no-code rule in the project as well, with `permissions.deny` rules on the labs folder and a
Socratic output style.

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
