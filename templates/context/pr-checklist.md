# Before and while creating a PR

Read by the `create-pr` and `triage-dependabot` skills. The skills hold the procedure; this
file holds the values.

## Repo and branches

- **Repo:** `<owner>/<name>`. **Base branch:** `<develop|main>`.
- **Branch names:** <scheme, e.g. `feat/<issue>-…`>
- **Protected:** <branches> need <required checks>.
- **Merge method:** <merge commit | squash>. **Commit subjects:** <format>.

## Dependabot

- Check open alerts (`gh api repos/<owner>/<name>/dependabot/alerts`) and open version-update
  PRs before every PR. Blocking = failing CI, conflicts, or an unproven major/core bump.
- **Core dependencies:** <list>
- **Lock file:** <command that regenerates it>; then <re-test commands>.
- **Known recurring cases:** <e.g. linter bumps that add rules>

## Gates, in order

1. <compliance/security quick check, if any>
2. <lint command>
3. <test command and coverage rule>
4. <e2e command, and the paths that make it necessary>

## Docs

- Run `update-documentation` (doc map: `.claude/context/docs.md`).
- <docs-touched check, and the opt-out line/label when there is no doc change>
