# Codex continuation

## Desktop

Use the tools actually registered by the host; discover deferred tools by their documented names. These are conversation tools, not Git or GitHub operations.

1. Call `list_projects` before choosing a project target. Match the current workspace to a returned project ID; do not guess IDs. Choose local execution unless the user requested a worktree. A projectless target creates a separate output directory: use it only if the continuation has access to the original artifacts. If workspace access is missing, keep transfer pending and resolve that dependency.
2. Call `create_thread` once with a clear title and human-readable prompt. Using this skill at the user's request explicitly asks for a new conversation. Omit `model` and `thinking` only when the destination defaults are established to match the effective source selection. Pass through a known explicit user selection when supported. If the selection/defaults cannot be established or preserved, keep the transfer pending and report that dependency; do not guess values or silently switch settings. Include the existing workspace and handoff paths in the private prompt, never in public repository content.
3. A returned `threadId` identifies a ready conversation. A `clientThreadId` means setup is pending: do not pass it to tools requiring `threadId`. Use native list/status tools to resolve the ready thread before waiting. Preserve the pending identity so uncertainty cannot trigger duplicate creation.
4. Observe with `wait_threads`, preserving each target's `hostId` and returned cursor as `afterCursor`. Use bounded waits (at most 60 seconds per call) and continue observing while setup/execution progresses. `timedOut: true` can coexist with active work; it is not failure. If compact progress does not establish that the document was read and the next action started, use a bounded `read_thread` to examine available evidence. Report an unverified start as unverified. Stop monitoring once start or a concrete blocker is established; do not wait for the whole task to finish.
5. Return the native created-thread directive on its own line, using the confirmed ID:

   `::created-thread{threadId="..."}`

   For setup still pending, use `::created-thread{clientThreadId="..."}` and state that continuation has not yet been verified.

Do not send follow-up messages to an existing conversation unless the user explicitly authorized messaging it. The initial `create_thread` prompt should contain everything needed to start.

## CLI

When desktop conversation tools are unavailable, inspect `codex exec --help` and use a durable host-managed process from the original workspace:

```sh
codex exec --json -C "$workspace" "$continuation_prompt"
```

This creates a fresh persisted session. Record the thread identity from `thread.started`, then inspect subsequent item/turn events for handoff-read and next-action evidence. Preserve verified model, reasoning effort, profile, sandbox, and approval restrictions through supported session options. Do not use `--ephemeral`, `--ignore-user-config`, `--ignore-rules`, or bypass flags. Do not create a worktree just to launch a continuation. If unattended execution cannot service an approval gate, report that gate rather than loosen the policy.

Use the same managed process and recorded session identity to reconcile ambiguous results. A confirmed session can later be continued with the installed CLI's `codex resume` or `codex exec resume` interface. If no permitted durable launcher exists, deliver the handoff and prompt without claiming a start.

Source: [Codex non-interactive mode](https://developers.openai.com/codex/noninteractive).
