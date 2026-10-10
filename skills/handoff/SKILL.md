---
name: handoff
description: Transfer ongoing work to a new conversation when the user requests a handoff or continuation in a fresh session. Reach a recoverable checkpoint, write a concise handoff, and launch the continuation when session tools are available.
---

# Handoff

Carry the user's existing objective into a fresh conversation. A request to use this skill authorizes creating that continuation; it does not expand the work's permissions or authorize unrelated actions. Arguments describe the next session's focus. Preserve any explicit stop, cancellation, read-only, or approval boundary.

## Reach a handoff point

Inspect the current task, pending tools, background processes, and delegated work before summarizing. If work is underway, finish only the smallest bounded step needed to make it recoverable. Do not finish the whole project or begin a new feature just to hand off.

A handoff point has known artifact state, recorded verification results, no uncontrolled writer, and a concrete next action. Examples include a completed edit with its affected check, a finished diagnostic experiment with captured evidence, or an external operation whose result has been reconciled. Uncommitted changes are acceptable when their ownership and state are clear; do not commit or push just for a checkpoint.

For an in-flight non-interruptible operation, wait using the platform's bounded wait tools and provide meaningful progress updates. For a long-running writer, use its supported pause/stop and reconcile partial output if safe. Read-only processes may continue if their IDs, output locations, and collection steps are recorded. Do not leave delegated writers able to modify the same artifacts after transfer.

If an external effect remains unknown, record the operation identity and uncertainty, and make reconciliation the successor's first action; never retry it merely to obtain a clean checkpoint. If a safe boundary cannot be reached, explain the specific blocker and keep the transfer pending. An explicit user instruction to stop immediately overrides finishing the current step: stop safely and record partial state.

## Write the handoff

Save a uniquely named Markdown document outside the workspace, in the OS temporary directory (`$TMPDIR`, else `/tmp`; `%TEMP%` on Windows). Use a private directory/file where supported. Read it back before dispatch. Keep it available until the successor has read it.

Include only what the successor needs:

- The user's objective, latest steering, next-session focus, and existing permissions and pending gates.
- Workspace/project identity, branch and revision when applicable, local changes, and relevant artifact paths or URLs.
- Completed work with evidence, failed or skipped checks, unresolved questions, and known versus unknown external results.
- The checkpoint, process/task ownership, remaining work, and the first executable next action.
- A suggested-skills section naming available skills to load through the host's skill mechanism, with a brief reason.

Reference existing specs, plans, decisions, issues, commits, and diffs instead of copying them. Preserve decisions and causal context that those artifacts omit. Redact secrets and unnecessary personal data; retain local resource paths only when needed for the authorized same-host continuation. Never publish the handoff to a repository or external destination as part of this workflow.

## Launch and transfer ownership

Use the host's native conversation-creation tools. Do not use a subagent as a substitute for the user's new conversation. For Codex desktop, read [references/codex.md](references/codex.md) before dispatch.

Choose the existing project and execution environment where the artifacts are accessible. Keep the configured model and effort by default; set overrides only when the user explicitly requested them and the destination supports them. Do not invent branch names or create a worktree unless requested.

Send a self-contained continuation prompt: read the handoff first, verify the current state and applicable instructions, then begin its next action within the inherited scope. Include the objective, document location, checkpoint, and authorization limits in the prompt so the new session can detect missing or stale material. Do not require another confirmation for already authorized work. If the document cannot be shared with the destination, resolve access before launching.

Freeze source-side writes before creation. Record the returned conversation/operation identity and observe the existing continuation until its setup completes and there is evidence that it read the handoff and began the next action, or reached a concrete user-input/blocking boundary. Creation alone proves dispatch, not execution or completion. Once launched, the successor owns execution; the source only monitors or answers the user.

On a timeout or ambiguous create result, reconcile through native status/list tools rather than creating another conversation. Reuse a continuation already created for this transfer. If creation definitively fails before launch, source ownership remains; report the blocker. If creation tools are unavailable, deliver the document and ready-to-send prompt, and state that automatic continuation is unavailable. Do not claim a completed transfer.

Report the checkpoint, handoff location, continuation identity, and observed start or blocker. Do not invoke this skill recursively in the successor just because it was used by the source.
