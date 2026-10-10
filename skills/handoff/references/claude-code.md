# Claude Code (CC)

Load the skill through Claude Code's skill mechanism. Global installation uses `~/.claude/skills/handoff`; invoke `/handoff` or request the skill in natural language. `agents/openai.yaml` is optional Codex UI metadata, not a dependency.

## Start a fresh persisted session

Prefer a native top-level session launcher if the environment exposes one. Otherwise inspect `claude --help` and use the installed CLI from the original workspace. Do not use the Task/subagent tool or `--agent` to create a substitute worker.

`claude --bg "$continuation_prompt"` can return a fresh background session identity, but some versions automatically isolate edits in a new worktree. Use it only when the installed version and already-effective configuration provably preserve the original workspace, including uncommitted checkpoint artifacts. Otherwise use the managed print-mode path below. Do not create a worktree or change isolation settings for the handoff. A background session is a separate persisted conversation; a subagent is not.

Use a host-managed process with a fresh UUID and persisted print mode when native background launch cannot preserve the workspace:

```sh
claude -p --session-id "$new_session_id" --output-format stream-json --verbose "$continuation_prompt"
```

The shell variables represent separately constructed values; use argument arrays when launching programmatically. Do not disable session persistence. Carry forward known model, effort, permission-mode, and tool restrictions using supported options; never use `--dangerously-skip-permissions`, `--bare`, or `--safe-mode` to bypass the source environment. Respect workspace trust, approval prompts, and any nested-launch restriction; do not unset guard variables to circumvent them.

Observe private stream output for the matching session identity, handoff read, and first next-action evidence. An initialization event proves startup only. If a permission request blocks the next action, report it as a gate. If start is uncertain, inspect that same session/process, never spawn another. The user can later open a confirmed persisted session with `claude --resume "$new_session_id"`.

If the host lacks a durable managed process or the CLI cannot start within the current permissions, return the handoff and prompt with the specific dependency. Do not claim automatic continuation or change Claude settings/hooks.

Sources: [Claude Code CLI reference](https://code.claude.com/docs/en/cli-reference), [Background session isolation](https://code.claude.com/docs/en/agent-view#how-file-edits-are-isolated).
