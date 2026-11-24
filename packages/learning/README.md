# Socratic learning skills

Install per project, and only in a learning repo: `docs/USING.md`, "Add the learning package".
Source: `packages/learning/.apm/`. Deployed copies: the project's `.claude/skills/` and
`.claude/agents/` (never edit them there).

The package is opt-in. A project gets it only if its own `apm.yml` lists
`LasterBergamot/ai-common/packages/learning#<tag>`. Projects that list only `workflow` never see
these skills.

## The rule every primitive follows

The AI tutors and organises; the learner writes the code. No skill or agent writes lab code,
Dockerfiles, compose files, manifests, IaC, scripts or configs. They write Markdown only:
briefs, notes, logs, the review queue and docs pages. A project can enforce this with
`permissions.deny` rules on the labs folder and a Socratic output style. This package only
supplies the skills.

## What it installs

Every primitive declares `model:` and `effort:` (see `docs/AUTHORING.md`).

| Primitive | Purpose | model | effort |
|---|---|---|---|
| `socratic-tutor` | A learning session: probe, question, hint ladder, explain-back, summary | opus | high |
| `plan-lab` | Topic → lab brief, prerequisites, place in the learning-path graph, issue drafts | opus | high |
| `assessment-prep` | `<subject> <level>`: diagnose, coach the gaps, mock evaluation, update readiness | opus | high |
| `import-matrix` | Pasted competency matrix → verbatim copy + normalised table with IDs + readiness tracker | sonnet | medium |
| `session-log` | Session → the notes in the learner's words, recall questions, follow-up issue drafts | sonnet | medium |
| `review-quiz` | Spaced-repetition quiz from the review queue, then reschedules | sonnet | medium |
| `roadmap-sync` | Issue states and readiness → Mermaid status classes and heatmaps, only inside the generated markers | haiku | medium |
| agent `socratic-reviewer` | Reviews the learner's code with questions, never with fixes. Read-only (Read, Grep, Glob) | sonnet | high |

## Project facts

Every skill reads `.claude/context/learning.md`: paths, tutor tone, the hint ladder, tracks,
levels, platforms, the log and review-queue formats, and the visualization markers. The
template is `templates/context/learning.md`. Skills that draft issues also read
`.claude/context/issues.md`, and create issues through the `workflow` package's `new-issue`
conventions, so install `workflow` alongside.
