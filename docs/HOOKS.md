# Guard hooks

The hooks live in `claude-code/hooks/` and are installed to `~/.claude/hooks/` by `dev-config`.
`pretool.sh` is the entry point for every tool call: it reads the call once, logs it, runs the
guards in order (the first one to ask ends the call), then backs up a file about to change.

Most guards **ask** rather than block: Claude Code shows a confirmation prompt, even in auto
mode. The guard scripts say which of them block outright.

| Hook | Event | What it does |
|------|-------|--------------|
| `pretool.sh` | PreToolUse | Entry point. Runs the guards in order. Set `onFailure: block` in `settings/hooks.json`, so a crash blocks the call instead of letting it through. |
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
| `notify.sh` | Notification, Stop | Shows a notification when Claude is waiting for permission or has finished. |

## Changing a rule

1. Edit the script in `ai-common/claude-code/hooks/`.
2. Run `bash claude-code/hooks/test-hooks.sh` (Git Bash on Windows).
3. Re-run `dev-config`'s installer so `~/.claude/hooks/` is updated.

## Machine-specific values

Put drives, services and paths that differ per machine in `~/.claude/hooks/local.sh`. Never
commit that file. `local.sh.example` shows the shape.
