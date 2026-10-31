# Guard hooks

Installed per machine from `packages/guards` (see `USING.md`, step 1). The scripts end up in
`~/.claude/hooks/guards/scripts/`; the internals are in
[`packages/guards/README.md`](../packages/guards/README.md).

`pretool.sh` is the entry point for every tool call: it reads the call once, logs it, runs the
guards in order (the first one to ask ends the call), then backs up a file about to change.
It is registered with `onFailure: "block"`, so a crashing guard blocks the call instead of
letting it through.

Most guards **ask** rather than block: Claude Code shows a confirmation prompt, even in auto
mode.

| Hook | Event | What it does |
|------|-------|--------------|
| `pretool.sh` | PreToolUse | Entry point. Runs the guards below in order. |
| `guard-git.sh` | PreToolUse | Asks before a commit, push, merge or release. |
| `guard-delete.sh` | PreToolUse | Asks before a file or resource is deleted, moved away or overwritten. Deletes inside temp folders pass. |
| `guard-secrets.sh` | PreToolUse | Asks before a file that holds secrets is read or changed, and before a credential-shaped value goes on the command line. |
| `guard-protect.sh` | PreToolUse | Asks before changes to protected locations, services, containers, running processes (any kill), and the hook and settings files. |
| `guard-workspace.sh` | PreToolUse | Asks before writes outside the working directory. Temp folders and `~/.claude` are allowed. |
| `guard-network.sh` | PreToolUse | Asks before uploads, POST requests, remote shells, code piped into an interpreter, and global package installs. |
| `audit.sh` | PreToolUse | Logging only: one line per call in `~/.claude/logs/`. Content is not logged, only its size. |
| `backup.sh` | PreToolUse | Never blocks: keeps a copy of a file before Write, Edit or NotebookEdit changes it, in `~/.claude/backups/<date>/`. |
| `posttool-check.sh` | PostToolUse | After Write, Edit or NotebookEdit: checks the syntax of the edited file. A failure goes back to Claude as the reason to fix it. It does not format. |
| `prompt-check.sh` | UserPromptSubmit | Warns when a prompt looks like it contains a credential. Never blocks. |
| `session-start.sh` | SessionStart | Informational: shows which guards are active and the repository state. |
| `notify.sh` | Notification, Stop | Windows only: a notification when Claude waits for permission or has finished. Does nothing on Linux. |

## Changing a rule

1. Edit the script in `packages/guards/scripts/`.
2. Run `bash packages/guards/scripts/test-hooks.sh` (15 Windows-path cases fail on Linux; see
   `AUTHORING.md`).
3. Release (`AUTHORING.md`) and reinstall: `apm install -g LasterBergamot/ai-common/packages/guards#<new tag> --target claude`.

## Machine-specific values

Put drives, services and paths that differ per machine in `~/.claude/hooks/local.sh`. Never
commit that file. `scripts/local.sh.example` shows the shape.
