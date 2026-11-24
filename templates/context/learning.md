# Learning conventions

Read by the `learning` package's skills (`socratic-tutor`, `plan-lab`, `assessment-prep`,
`import-matrix`, `session-log`, `review-quiz`, `roadmap-sync`) and the `socratic-reviewer`
agent. The skills hold the procedure; this file holds the values.

## Paths

- **Labs root:** `<labs/>`. Each lab is `<labs/>/<track>/<lab>/`.
- **Lab template:** `<labs/_template/>` (files: `<README.md, NOTES.md, QUESTIONS.md, TEARDOWN.md>`)
- **Assessments root:** `<assessments/>`. Each subject is `<assessments/>/<subject>/`.
- **Assessment template:** `<assessments/_template/>` (files: `<matrix.md, readiness.md, sessions/, mock/>`)
- **Docs folder:** `<docs/>` (<how it is published, e.g. mirrored flat to the GitHub wiki>)
- **Review queue:** `<docs/Review-Queue.md>`
- **Learning-paths page:** `<docs/Learning-Paths.md>`
- **Track page pattern:** `<docs/Track-<Name>.md>`
- **Assessments heatmap page:** `<docs/Assessments.md>`
- **Glossary:** `<docs/Glossary.md>`

## Tutor

- **Language:** <English>
- **Tone:** <e.g. patient, direct, curious; short questions; no praise inflation>
- **Hint ladder:** <1. a narrower question → 2. where to look → 3. the concept in prose → 4. the official doc link>
- **No-code rule:** never write lab code, Dockerfiles/Containerfiles, compose files, manifests,
  IaC, scripts, configs or certificates, under the labs root or in chat. Write Markdown only.
- **Allowed exceptions:** <naming an API, command, flag or config key; linking official docs;
  a short example to critique in assessment prep; …>

## Tracks

| ID | Label | Track page |
|----|-------|------------|
| `<foundations>` | <Foundations> | `<docs/Track-Foundations.md>` |

## Levels

- **Lab levels:** <`level:1-understand`, `level:2-apply`, `level:3-build`, `level:4-teach`>
- **Lab milestones:** <understand → build → break on purpose → fix → explain back>
- **Assessment levels** come from each subject's `matrix.md` (for example Novice / Intermediate /
  Advanced). They are cumulative: a level includes every level below it.
- **Re-check interval:** a `ready` competency is re-checked after <60> days.

## Platforms

| Platform | Engine | Shell | Notes |
|----------|--------|-------|-------|
| <Linux> | <Docker> | <bash> | <limits, e.g. disk> |
| <Windows 11> | <Podman> | <Git Bash> | <…> |

Lab rules that keep every platform working: <compose spec only, no host network, no host port
below 1024, relative bind mounts, LF line endings, …>

## Session logs

- **Lab sessions:** appended to the lab's `<NOTES.md>` under `## YYYY-MM-DD: <goal>`
- **Assessment sessions:** `<assessments/>/<subject>/sessions/YYYY-MM-DD-<level>.md`
- **Format:** Goal · What I can now explain (in my words) · What I built or changed (prose and
  links) · Misconceptions fixed · Still unclear · Next step

## Review queue

- **Format:** one table row per question: `| id | source | question | interval | due | last | history |`
- **Intervals (days):** <1, 3, 7, 14, 30>, after 30 stays at 30
- **IDs:** `<R-0001>`, increasing; never reused

## Visualization

- **Mermaid classDef names:** `todo`, `learning`, `building`, `done`, with colours
  <`todo fill:#eee`, `learning fill:#fde68a`, `building fill:#93c5fd`, `done fill:#86efac`>
- **Board Status → class:** <Todo → todo, Learning → learning, Building → building, Review → building, Done → done>
- **Generated-block markers:** `<!-- roadmap-sync:start -->` … `<!-- roadmap-sync:end -->`.
  Only `roadmap-sync` writes between them.
- **Readiness statuses and emoji:** `not-started` <⬜>, `learning` <🟨>, `can-explain` <🟦>, `ready` <🟩>
