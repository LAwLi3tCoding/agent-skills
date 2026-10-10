# OpenCode

OpenCode discovers the standard `SKILL.md` format in `.agents/skills`, `.opencode/skills`, and their documented global locations. Load `handoff` through its `skill` tool or a natural-language request; do not assume a skill automatically registers a `/handoff` command.

Before creating a session through either API or CLI, establish from the effective configuration and relevant environment flags that automatic sharing is disabled. Omitting `--share` is insufficient: `share: auto` and auto-share environment options can publish a newly created session without that flag. If sharing is enabled or its effective state cannot be established, keep the transfer pending and report the privacy dependency. Do not change global/server configuration or expose the private handoff to test it.

## Session API

If an authorized OpenCode server/SDK is already available, use its current schema and workspace context. `POST /session` creates a session; omit `parentID` for a top-level continuation. Record the returned ID before sending the continuation with `POST /session/:id/prompt_async` (or `/message`). Use `GET /session/status` and `GET /session/:id/message` to observe the existing session. Creation and prompt acceptance are separate receipts; a `204` response does not prove the next action started.

Keep the effective model, variant, agent, workspace, and permission restrictions. Do not treat a new session's configured defaults as proof that session-local restrictions were inherited. If those restrictions cannot be preserved through the installed interface, keep the transfer pending. Use only the authorized server endpoint; do not enable public sharing, expose a new unauthenticated server, or change server configuration for a handoff.

## CLI alternative

Inspect `opencode run --help`, then start a fresh persisted session through a durable host-managed process from the source workspace:

```sh
opencode run --format json "$continuation_prompt"
```

The prompt includes the handoff path and directs the successor to read it first. Omit `--continue`, `--session`, and `--fork` for the initial fresh run. Carry forward verified source settings with supported `--model`, `--variant`, and `--agent` options as needed. Do not use `--auto` or `--share`.

Capture the returned session ID from JSON events and observe handoff-read/next-action evidence. If the result is ambiguous, use the same process or `opencode session list --format json` and inspect the matching session; do not relaunch. A confirmed session can be opened interactively with `opencode --session "$session_id"`. If neither a session API nor durable managed CLI run is available, report the dependency and deliver a ready-to-send prompt.

Sources: [Agent Skills](https://opencode.ai/docs/skills/), [CLI](https://opencode.ai/docs/cli/), [Server API](https://opencode.ai/docs/server/), [Session sharing](https://opencode.ai/docs/share/).
