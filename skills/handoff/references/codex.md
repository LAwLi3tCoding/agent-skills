# Codex desktop continuation

Use the tools actually registered by the host; discover deferred tools by their documented names. These are conversation tools, not Git or GitHub operations.

1. Call `list_projects` before choosing a project target. Match the current workspace to a returned project ID; do not guess IDs. Choose local execution unless the user requested a worktree. A projectless target creates a separate output directory: use it only if the continuation has access to the original artifacts. If workspace access is missing, keep transfer pending and resolve that dependency.
2. Call `create_thread` once with a clear title and human-readable prompt. Using this skill at the user's request explicitly asks for a new conversation. Omit `model` and `thinking` to use the user's configured defaults; override only for an explicit user selection. Include the existing workspace and handoff paths in the private prompt, never in public repository content.
3. A returned `threadId` identifies a ready conversation. A `clientThreadId` means setup is pending: do not pass it to tools requiring `threadId`. Use native list/status tools to resolve the ready thread before waiting. Preserve the pending identity so uncertainty cannot trigger duplicate creation.
4. Observe with `wait_threads`, preserving each target's `hostId` and returned cursor as `afterCursor`. Use bounded waits (at most 60 seconds per call) and continue observing while setup/execution progresses. `timedOut: true` can coexist with active work; it is not failure. If compact progress does not establish that the document was read and the next action started, use a bounded `read_thread` to examine available evidence. Report an unverified start as unverified. Stop monitoring once start or a concrete blocker is established; do not wait for the whole task to finish.
5. Return the native created-thread directive on its own line, using the confirmed ID:

   `::created-thread{threadId="..."}`

   For setup still pending, use `::created-thread{clientThreadId="..."}` and state that continuation has not yet been verified.

Do not send follow-up messages to an existing conversation unless the user explicitly authorized messaging it. The initial `create_thread` prompt should contain everything needed to start.
