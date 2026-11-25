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
4. **Generic skills read project context files** (`.agents/context/*.md`, or
   `.claude/context/*.md` when the project has no `.agents/context/`; changed in v0.3.0). That is how one
   skill serves several projects, and why the project-specific skills (for example lineup's
   `compliance-audit` and `supabase-smoke`) stay in their project.
5. **Path-scoped rules stay in each project's `.claude/rules/`.** They are always
   project-specific. APM's `instructions` primitive (`applyTo` globs) would deploy to
   `.claude/rules/` for Claude, but nothing generic needs it yet.
6. **Targets: `claude` + `agent-skills` now, other tools by adding targets** (v0.3.0; replaces
   "claude only"). The skills are written in neutral wording ("read", "search the web", "run"),
   and every tool reads the project's `AGENTS.md` router. Details below, under
   "Multiple AI tools".
7. **CI does not run `apm audit` yet**: it would need a token for this private repo. Run it
   locally before committing an update.

## Known limits

- Dependabot doesn't know about APM dependencies: bumping the tag is manual (`USING.md`).
- `apm install` refuses to overwrite a skill it didn't write; delete the old copy first.

## Multiple AI tools (tested with APM 0.35.0, v0.3.0)

**A package's `targets:` is the set it may deploy to.** A project's `targets:` (or `--target`)
picks from that set. With a package listing only `claude`, a project asking for `gemini` got
nothing. So `workflow` and `learning` list `claude`, `agent-skills`, `gemini` and `codex`.
`guards` stays `claude` only.

What each target deploys, from one source:

| Target | Skills | Agents | Context file (`apm compile`) |
|--------|--------|--------|------------------------------|
| `claude` | `.claude/skills/<name>/SKILL.md` | `.claude/agents/<name>.md` | `CLAUDE.md` |
| `agent-skills` | `.agents/skills/<name>/SKILL.md` | none | `AGENTS.md` |
| `gemini` | `.agents/skills/` (shared with agent-skills) | none | `GEMINI.md` (`@./AGENTS.md`) |
| `codex` | `.agents/skills/` | `.codex/agents/<name>.toml` | `AGENTS.md` |

The skill files are byte-identical in `.claude/skills/` and `.agents/skills/`: APM copies them
and other tools ignore the frontmatter keys they don't know.

**What APM does not translate** (Claude-only, by nature):
- Frontmatter: `effort:` and `tools:` are dropped for Codex agents (APM warns: "lossy agent
  compilation"). `model:` is copied as-is, so a Codex agent gets `model = "sonnet"`, a name
  Codex doesn't know. Before enabling `codex` in a project, check the generated TOML.
- Hooks: the `guards` package parses Claude Code's hook payload. Another tool needs a payload
  adapter.
- Output styles and `permissions` rules (`.claude/settings.json`): Claude-only. Put the rules
  every tool must follow in `AGENTS.md`; the Claude settings are an extra hard layer.
- Claude tool names written in a skill's text (`Read`, `Grep`, `WebSearch`): avoid them
  (`AUTHORING.md`). `tools:` in an agent's frontmatter is fine; it is metadata.

**`apm compile` and handwritten routers.** `apm compile` builds `AGENTS.md`, `CLAUDE.md` and
`GEMINI.md` from `instructions` primitives. It **never overwrites a hand-authored** `AGENTS.md`
or `CLAUDE.md` ("Protected AGENTS.md: hand-authored file will not be overwritten"). It only
creates missing ones, for example a `GEMINI.md` containing `@./AGENTS.md`. Our projects write
`AGENTS.md` by hand (template: `templates/AGENTS.md`) with `CLAUDE.md` as a stub importing it.
Running `apm compile` is optional: it is a way to get the `GEMINI.md` stub.

