# APM evaluation (lineup#157)

Decision so far: ai-common is the home of the tool-agnostic setup. Whether APM is the
compiler for it is still open.

## What APM is

[microsoft/apm](https://github.com/microsoft/apm) reads one manifest, `apm.yml`, and installs
instructions, skills, agents, prompts and MCP servers for several agent tools (Copilot, Claude
Code, Cursor, Codex, Gemini and others). `apm.lock.yaml` pins the resolved tree, and
`apm audit` checks for drift and hidden Unicode.

## Open questions (from lineup#157)

1. How do path-scoped rules and the Claude-only `model:` and `effort:` frontmatter map to the
   other tools?
2. Is compiled output committed, or generated in CI?
3. Where does `apm audit` fit next to the docs check?

## Next step

Install `apm` (Linux: `curl -sSL https://aka.ms/apm-unix | sh`), then in a scratch project run
`apm compile` against `apm.yml` and check what it writes. Record the answers above here.
