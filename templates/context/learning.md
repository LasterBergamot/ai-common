# Learning conventions

Read by the `learning` package's skills (`learn`, `socratic-tutor`, `plan-lab`, `prereq-check`,
`assessment-prep`, `import-matrix`, `session-log`, `review-quiz`, `roadmap-sync`) and the
`socratic-reviewer` agent. The skills hold the procedure; this file holds the values. It lives
in `.claude/context/learning.md`.

## Paths

- **Labs root:** `<labs/>`. Each lab is `<labs/>/<track>/<lab>/`.
- **Lab template:** `<labs/_template/>` (files: `<README.md, NOTES.md, QUESTIONS.md, TEARDOWN.md>`)
- **Track README pattern:** `<labs/<track>/README.md>` (why, prerequisites, graph, lab table, References)
- **Root README:** `<README.md>` (router and the track-level progress graph)
- **Assessments root:** `<assessments/>`. Each subject is `<assessments/>/<subject>/`.
- **Assessment template:** `<assessments/_template/>` (files: `<README.md, matrix.md, readiness.md, sessions/, mock/>`)
- **Subject references file:** `<assessments/<subject>/README.md>` (its References section)
- **Docs folder:** `<docs/>` (plain Markdown in the repo, relative links)
- **Review queue:** `<docs/Review-Queue.md>`
- **Assessments heatmap page:** `<docs/Assessments.md>`
- **Glossary:** `<docs/Glossary.md>`
- **Status command:** `<python3 scripts/lab-status.py>` (read-only; prints the labs in
  progress with where the learner stopped and the next step, due reviews, assessment
  readiness, labs ready to start). Optional: without one, `learn` reads the files itself.

## Tutor

- **Language:** <English>
- **Tone:** <e.g. patient, direct, curious; short questions; no praise inflation>
- **Hint ladder:** <1. a narrower question → 2. where to look → 3. the concept in prose → 4. the official doc link>
- **No-code rule:** never write lab code, Dockerfiles/Containerfiles, compose files, manifests,
  IaC, scripts, configs or certificates, under the labs root or in chat. Write Markdown only.
- **Allowed exceptions:** <naming an API, command, flag or config key; linking official docs;
  a short example to critique in assessment prep; …>
- **Picking up a track or lab:** run `prereq-check` first (repo, then web). It advises; the
  learner decides.
- **Entry point:** `learn`, started at the repo root. It shows where the learner stands,
  recommends, hands over to the other skills and moves the shell into the chosen lab folder.
- **Recommendations:** at most <5>, each with its reason (graph, a done lab it builds on, an
  assessment gap, an open question, a web source) and its cost (tier, RAM, disk).
- **Interview:** <only about learning: what to understand or build, how deep, how much time.
  Asked in the session, never stored.>
- **Sources:** every online source used for a topic, track or lab goes into the **References**
  section of the relevant README (lab README, track README, or the subject references file).

## Tracks

| ID | Label | Track README |
|----|-------|--------------|
| `<foundations>` | <Foundations> | `<labs/foundations/README.md>` |

## Levels

- **Lab levels:** <`level:1-understand`, `level:2-apply`, `level:3-build`, `level:4-teach`>
- **Lab milestones:** <understand → build → break on purpose → fix → explain back>
- **Assessment levels** come from each subject's `matrix.md` (for example Novice / Intermediate /
  Advanced). They are cumulative: a level includes every level below it.
- **Re-check interval:** a `ready` competency is re-checked after <60> days.

## Platforms

Every instruction, script and teardown covers **all** platforms below in the same change.

| Platform | Engine | Shell | Notes |
|----------|--------|-------|-------|
| <Linux> | <Docker> | <bash> | <limits, e.g. disk> |
| <Windows 11> | <Podman> | <Git Bash> | <…> |

Lab rules that keep every platform working: <compose spec only, no host network, no host port
below 1024, relative bind mounts, LF line endings, …>

## Session logs

- **Lab sessions:** appended (newest last) to the lab's `<NOTES.md>` under
  `## Session YYYY-MM-DD: <goal>`
- **Assessment sessions:** `<assessments/>/<subject>/sessions/YYYY-MM-DD-<level>.md`
- **Format:** one `- **Label:** text` bullet per field, these labels, this order:
  `Goal` · `What I can now explain` (in my words) · `What I built or changed` (prose and
  links) · `Misconceptions fixed` · `Still open` · `Where I stopped` · `Next step`. The last two
  are never left out while a lab is in progress; the status command and `learn` read them.
- **Checkpoint commit:** at the end of a session, propose `git add` of the lab or subject
  folder, a commit `<<track>/<lab>: <what was done>>` and a push. Only after an explicit
  go-ahead.

## Review queue

- **Format:** <the table columns, and the markers that delimit the queue, if any>
- **Intervals (days):** <1, 3, 7, 14, 30>; a miss goes back to the first interval
- **IDs:** `<R-0001>`, increasing; never reused (if the format uses IDs)

## Visualization

- **Status line:** each lab README has `<**Status:** <value>>` under its title. Values:
  <`todo`, `learning`, `building`, `done`> (understand → learning; build, break, fix →
  building; explained back → done). A planned lab with no folder yet counts as `todo`.
- **Track status:** <done when all labs are done; building when any is building; learning when
  any is learning; otherwise todo>
- **Node ids:** <a track node's id is the track id; a lab node's id is the lab id with `-`
  replaced by `_` (lab `ml-math` → node `ml_math`)>. The skills match a node to its lab by
  this rule, never by position.
- **Mermaid classDef names:** `todo`, `learning`, `building`, `done`, with colours
  <`todo fill:#eee`, `learning fill:#fde68a`, `building fill:#93c5fd`, `done fill:#86efac`>
- **Generated-block markers:** `<!-- roadmap-sync:start -->` … `<!-- roadmap-sync:end -->`.
  Inside them, only `roadmap-sync` writes the `class` lines; nodes and edges are added by
  `plan-lab`, `prereq-check` or the learner.
- **Issues (optional):** <whether labs are also tracked as GitHub issues; the Status line is
  the source of truth either way>
- **Readiness statuses and emoji:** `not-started` <⬜>, `learning` <🟨>, `can-explain` <🟦>, `ready` <🟩>
