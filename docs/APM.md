# APM decisions

Answers to the open questions from lineup#157, settled while setting this up (APM 0.35.0).

## What APM does for us

`apm install` reads `apm.yml`, fetches each dependency at its pinned ref (into
`apm_modules/`), and deploys its primitives into the target's folders. `apm.lock.yaml`
records the resolved commit and a SHA-256 for every deployed file. `apm audit` re-checks those
hashes (drift) and scans for hidden Unicode in the deployed content.

## Decisions

1. **Two packages in one repo.** `workflow` is installed per project; `guards` per machine.
   Hooks don't belong in a project: in a shared repo they would run on every contributor's
   machine. APM installs a sub-folder of a repo as a package
   (`LasterBergamot/ai-common/packages/<name>#<tag>`).
2. **Deployed files are committed in the project** (`.claude/skills/`, `.claude/agents/`), and
   `apm_modules/` is ignored. Without that, every contributor and CI would need access to
   this private repo just to have the skills.
3. **Pinned by tag**, never a branch. The lock file holds the exact commit.
4. **Generic skills read project context files** (`.claude/context/*.md`). That is how one
   skill serves several projects, and why the project-specific skills (for example lineup's
   `compliance-audit` and `supabase-smoke`) stay in their project.
5. **Path-scoped rules stay in each project's `.claude/rules/`.** They are always
   project-specific. APM's `instructions` primitive (`applyTo` globs) would deploy to
   `.claude/rules/` for Claude, but nothing generic needs it yet.
6. **Target: `claude` only.** No second agent is planned (decided in #157 of lineup, and
   confirmed again for v0.3.0), so the skills keep Claude Code's frontmatter (`model:`,
   `effort:`, `tools:`) and the hooks parse Claude Code's payload. If that changes: other targets deploy skills to
   `.agents/skills/` as plain instructions and ignore the Claude-only keys; the hooks would
   need a payload adapter.
7. **CI does not run `apm audit` yet**: it would need a token for this private repo. Run it
   locally before committing an update.

## Known limits

- Dependabot doesn't know about APM dependencies: bumping the tag is manual (`USING.md`).
- `apm install` refuses to overwrite a skill it didn't write; delete the old copy first.
