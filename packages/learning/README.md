# Socratic learning skills

Install per project, and only in a learning repo: `docs/USING.md`, "Add the learning package".
Source: `packages/learning/.apm/`. APM deploys copies per target: `.claude/skills/` and
`.claude/agents/` (Claude Code), `.agents/skills/` (agent-skills and Gemini), `.codex/agents/`
(Codex). Never edit the deployed copies.

The package is opt-in. A project gets it only if its own `apm.yml` lists
`LasterBergamot/ai-common/packages/learning#<tag>`. Projects that list only `workflow` never see
these skills.

## The rules every primitive follows

- **The AI tutors and organises; the learner writes the code.** No skill or agent writes lab
  code, Dockerfiles, compose files, manifests, IaC, scripts or configs. They write Markdown
  only: briefs, notes, logs, the review queue, READMEs. A project can enforce this harder with
  tool-specific rules (for Claude Code: `permissions.deny` on the labs folder and an output
  style). This package only supplies the skills.
- **Prerequisites first:** picking up a track or lab runs `prereq-check` (this repo, then the web).
- **Sources:** every online source used goes into the References section of the relevant README.
- **Progress lives in the repo:** each lab README's `**Status:**` line. `roadmap-sync` turns it
  into Mermaid graph colours; GitHub issues are optional.
- **Every platform the project lists** (for example Linux/Docker and Windows 11 Git Bash/Podman)
  is covered in the same change.
- **Continue anywhere:** `session-log` ends with a proposed checkpoint commit and push, run
  only after a go-ahead.

## What it installs

Every primitive declares `model:` and `effort:` (see `docs/AUTHORING.md`). Claude Code honours
them; other tools ignore them (`docs/APM.md`).

| Primitive | Purpose | model | effort |
|---|---|---|---|
| `socratic-tutor` | A learning session: prerequisite check on pick-up, probe, question, hint ladder, explain-back, summary and sources | opus | high |
| `plan-lab` | Topic → prerequisite check → lab brief with Status line and References → node in the track graph → optional issue drafts | opus | high |
| `prereq-check` | Before a track or lab: repo prerequisites and their Status, then web sources; reports "ready" or gaps; never blocks | opus | high |
| `assessment-prep` | `<subject> <level>`: diagnose, coach the gaps, mock evaluation, update readiness, record sources | opus | high |
| `import-matrix` | Pasted competency matrix → verbatim copy + normalised table with IDs + readiness tracker | sonnet | medium |
| `session-log` | Session → notes in the learner's words, recall questions, sources, Status proposal, checkpoint commit proposal | sonnet | medium |
| `review-quiz` | Spaced-repetition quiz from the review queue, then reschedules | sonnet | medium |
| `roadmap-sync` | Lab Status lines and readiness → Mermaid status classes and heatmaps, only inside the generated markers | haiku | medium |
| agent `socratic-reviewer` | Reviews the learner's code with questions, never with fixes. Read-only (tools: Read, Grep, Glob) | sonnet | high |

## Project facts

Every skill reads `learning.md` from the project's context folder: `.agents/context/`, or
`.claude/context/` if that doesn't exist. It holds paths, tutor tone, the hint ladder, tracks,
levels, platforms, the log and review-queue formats, the Status values and the visualization
markers. The template is `templates/context/learning.md`. Skills that draft issues also read
`issues.md` from the same folder and follow the `workflow` package's `new-issue` conventions,
so install `workflow` alongside.
