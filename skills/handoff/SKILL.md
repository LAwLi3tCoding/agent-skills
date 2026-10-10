---
name: handoff
disable-model-invocation: true
argument-hint: "[--prompt-only] [next-session focus]"
description: Transfer ongoing work to a fresh session in Codex, Claude Code, OpenCode, or DeepSeek Harness (DSH) when the user requests a handoff. Reach a recoverable checkpoint, write a concise handoff, and launch the continuation through the host's supported session interface.
---

# Handoff

Carry the user's existing objective into a fresh conversation. A request to use this skill authorizes creating that continuation; it does not expand the work's permissions or authorize unrelated actions. Arguments describe the next session's focus. Preserve any explicit stop, cancellation, read-only, or approval boundary.

## Invocation and mode

Run only when the user explicitly invokes this skill or requests a handoff. Do not select it autonomously because a conversation is long or context is running low. Hosts that support invocation metadata must enforce `disable-model-invocation: true`; Codex uses `agents/openai.yaml` with `allow_implicit_invocation: false`. On other hosts, retain this explicit-user-request rule without claiming a native enforcement mechanism.

Default mode creates a continuation. If the user supplies `--prompt-only` as a mode argument (or explicitly asks for a prompt without launching), reach the same recoverable checkpoint and write the same handoff, then return a self-contained, ready-to-send continuation prompt. Remove the mode flag from the next-session focus. Include the objective, handoff location, first next action, and inherited limits in that prompt. Verify that the intended destination can access the document; if it cannot, provide the necessary redacted context inline or identify the access blocker.

In prompt-only mode, freeze task writes after preparing the materials, do not call any session creation or launch interface, and end the current turn. Report that no successor has started and execution is stopped pending the user's next instruction. Do not claim execution ownership has transferred, archive/close the conversation, or terminate unrelated processes. The checkpoint and existing stop instructions still apply.

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

In default mode only, select the actual host, not merely an installed executable. Read only its reference before dispatch:

| Host | Session interface |
| --- | --- |
| Codex | [references/codex.md](references/codex.md) — desktop conversation tools or persisted CLI execution |
| Claude Code (CC) | [references/claude-code.md](references/claude-code.md) — native background session or persisted CLI session |
| OpenCode | [references/opencode.md](references/opencode.md) — session API or persisted CLI run |
| DeepSeek Harness (DSH) | [references/dsh.md](references/dsh.md) — host session interface or SDK/headless profile |

Prefer native session tools; otherwise use the host's documented SDK/API or CLI when available and allowed. A managed CLI run with its own persisted session is a valid continuation even without a new UI tab. Do not use a subagent or switch to another AI tool as a substitute. Check installed-version capabilities before using optional flags; never invent tool names or APIs.

Choose the existing project and execution environment where the artifacts are accessible. Preserve the source's effective model, effort, agent/profile, and permission restrictions. Defaults are sufficient only when they match; carry forward known session overrides through supported options without changing global settings. Never guess unavailable values or choose a more permissive mode to make launch succeed. Do not invent branch names or create a worktree unless requested.

Send a self-contained continuation prompt: read the handoff first, verify the current state and applicable instructions, then begin its next action within the inherited scope. Include the objective, document location, checkpoint, and authorization limits in the prompt so the new session can detect missing or stale material. Do not require another confirmation for already authorized work. If the document cannot be shared with the destination, resolve access before launching.

Freeze source-side writes before creation. Record the returned conversation/operation identity and observe the existing continuation until its setup completes and there is evidence that it read the handoff and began the next action, or reached a concrete user-input/blocking boundary. Creation alone proves dispatch, not execution or completion. Once launched, the successor owns execution; the source only monitors or answers the user.

For CLI launches, use the host's managed background/process facility and retain the process handle, private output location, and eventual session ID. A PID or process exit alone does not establish continuation start. Build argument arrays or safely quote arguments; never evaluate prompt contents as shell code. Do not use untracked shell backgrounding, permission-bypass flags, public session sharing, or configuration migrations. Leave active successors running in a facility that survives the source turn; if the host cannot provide that, do not launch an unsupervised writer.

On a timeout or ambiguous create result, reconcile through native status/list tools or captured process/session output rather than creating another conversation. Reuse a continuation already created for this transfer. If creation definitively fails before launch, source ownership remains; report the blocker. If no permitted launch interface is available, deliver the document and ready-to-send prompt, and state that automatic continuation is unavailable. Do not claim a completed transfer.

Report the checkpoint, handoff location, continuation identity, and observed start or blocker. Do not invoke this skill recursively in the successor just because it was used by the source.
