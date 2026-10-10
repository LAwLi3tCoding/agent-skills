# Agent Skills

**English** · [简体中文](README.zh-CN.md)

Reusable skills for coding agents, with a focus on reliable task continuity. Each skill can be installed independently. The same skill instructions support **Codex, Claude Code (CC), OpenCode, and DeepSeek Harness (DSH)**. Platform references provide the session-launching details.

## Available skills

| Skill | Purpose |
| --- | --- |
| [handoff](skills/handoff/SKILL.md) | Bring ongoing work to a recoverable checkpoint, prepare a handoff document, and start a new conversation to continue the task. |

## Installation

Install `handoff` globally for Codex, Claude Code, and OpenCode with the [skills CLI](https://github.com/vercel-labs/skills):

```sh
npx skills@latest add LAwLi3tCoding/agent-skills --skill handoff --agent codex claude-code opencode --global --yes
```

DSH also discovers the shared `~/.agents/skills/handoff` directory created by this installation when its filesystem skill provider is enabled. If `DSH_AGENTS_HOME` is customized, use that configured root instead. A higher-priority copy of `handoff` can shadow the shared installation; update the active copy rather than keeping divergent versions. Installation does not change models, profiles, permissions, or hooks.

Start a fresh turn or session if the host has not refreshed its skill catalog.

To select skills and a supported agent interactively:

```sh
npx skills@latest add LAwLi3tCoding/agent-skills
```

For installations managed by the skills CLI, update `handoff` with:

```sh
npx skills@latest update handoff --global --yes
```

## Usage

Request a handoff and describe what the next conversation should focus on. In Codex:

```text
$handoff Continue the remaining implementation in a new conversation, preserving the existing acceptance criteria.
```

In Claude Code, use `/handoff` with the same task description. In OpenCode and DSH, ask the agent to load the `handoff` skill and continue the task in a fresh session; available UI shortcuts depend on the host.

For prompt-only mode:

```text
$handoff --prompt-only Continue the remaining implementation with the existing acceptance criteria.
```

Use `/handoff --prompt-only ...` in Claude Code, or explicitly request the same mode when loading the skill in OpenCode or DSH. This mode prepares the handoff and a ready-to-send prompt, stops task execution, and ends the current turn without starting a successor or closing the conversation. Omit the flag to create a continuation by default.

The skill requires an explicit user request. Claude Code's `disable-model-invocation` and Codex's `allow_implicit_invocation: false` provide host-level invocation controls; other hosts follow the same instruction where equivalent controls are unavailable.

The request authorizes the continuation within the existing task scope. Pending approvals, read-only constraints, and other authorization boundaries carry forward.

## Handoff workflow

1. **Reach a recoverable checkpoint.** Inspect active tools, background processes, and delegated work. Finish the smallest bounded step needed for a clear task state; do not wait for the entire project to finish. Reconcile uncertain external results before retrying an operation.
2. **Prepare the handoff.** Write a concise document in a private temporary location outside the workspace. Record the objective, decisions, artifact state, verification evidence, open questions, and next action. Reference existing artifacts and redact secrets and unnecessary personal data.
3. **Start the continuation (default mode).** Freeze source-side writes before creating the new conversation. The successor reads the handoff, verifies the current state, and continues within the inherited scope. The source observes the transfer without continuing to modify shared artifacts.
4. **Verify the transfer (default mode).** Distinguish conversation creation from an observed start. Reuse the existing continuation when setup is pending or a result is ambiguous; do not create duplicates on timeout.

A checkpoint may include documented uncommitted changes. The skill does not commit or push code solely to prepare a handoff. If a safe checkpoint cannot be reached, it reports the blocker and leaves the transfer pending.

## Runtime requirements

All four hosts use the same checkpoint and ownership rules. Automatic continuation requires a permitted session interface, access to the handoff and workspace, and a way to preserve the source's effective configuration.

| Host | Continuation interface | Reference |
| --- | --- | --- |
| Codex | Desktop conversation tools or a persisted `codex exec` session | [Codex](skills/handoff/references/codex.md) |
| Claude Code (CC) | Native background session or a managed, persisted CLI session | [Claude Code](skills/handoff/references/claude-code.md) |
| OpenCode | Session API or a managed `opencode run` session | [OpenCode](skills/handoff/references/opencode.md) |
| DeepSeek Harness (DSH) | Host session interface, compatible headless profile, or existing SDK | [DSH](skills/handoff/references/dsh.md) |

The skill checks installed capabilities before selecting a launch path. A managed CLI session is a separate persisted conversation; it need not open a new UI tab. CLI processes require a durable host supervisor. DSH profile changes require verified equivalent model, tools, and permissions. The skill never substitutes a subagent or bypasses approval gates.

If no suitable launch interface is available, it produces the handoff document and a ready-to-send prompt, and reports that automatic continuation has not started. `agents/openai.yaml` supplies optional Codex UI metadata; other hosts use `SKILL.md` and its references directly.

The handoff document stays outside the repository. The skill reports the checkpoint, destination conversation, and observed start or blocker. A successful transfer does not establish that the underlying task is complete.

## Attribution and license

`handoff` is adapted from [Matt Pocock's handoff skill](https://github.com/mattpocock/skills/blob/main/skills/productivity/handoff/SKILL.md), extending it with checkpoint selection and active conversation continuation.

Distributed under the [MIT License](LICENSE), with the upstream copyright notice retained.
