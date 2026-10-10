# Agent Skills

**English** · [简体中文](README.zh-CN.md)

Reusable skills for coding agents, with a focus on reliable task continuity. Each skill can be installed independently. The current collection targets Codex, with native conversation support for Codex desktop.

## Available skills

| Skill | Purpose |
| --- | --- |
| [handoff](skills/handoff/SKILL.md) | Bring ongoing work to a recoverable checkpoint, prepare a handoff document, and start a new conversation to continue the task. |

## Installation

Install `handoff` globally for Codex with the [skills CLI](https://github.com/vercel-labs/skills):

```sh
npx skills@latest add LAwLi3tCoding/agent-skills --skill handoff --agent codex --global --yes
```

The skill is available in the next conversation turn after installation.

To select skills and a supported agent interactively:

```sh
npx skills@latest add LAwLi3tCoding/agent-skills
```

For installations managed by the skills CLI, update `handoff` with:

```sh
npx skills@latest update handoff --global --yes
```

## Usage

Request a handoff and describe what the next conversation should focus on:

```text
$handoff Continue the remaining implementation in a new conversation, preserving the existing acceptance criteria.
```

The request authorizes the continuation within the existing task scope. Pending approvals, read-only constraints, and other authorization boundaries carry forward.

## Handoff workflow

1. **Reach a recoverable checkpoint.** Inspect active tools, background processes, and delegated work. Finish the smallest bounded step needed for a clear task state; do not wait for the entire project to finish. Reconcile uncertain external results before retrying an operation.
2. **Prepare the handoff.** Write a concise document in a private temporary location outside the workspace. Record the objective, decisions, artifact state, verification evidence, open questions, and next action. Reference existing artifacts and redact secrets and unnecessary personal data.
3. **Start the continuation.** Freeze source-side writes before creating the new conversation. The successor reads the handoff, verifies the current state, and continues within the inherited scope. The source observes the transfer without continuing to modify shared artifacts.
4. **Verify the transfer.** Distinguish conversation creation from an observed start. Reuse the existing continuation when setup is pending or a result is ambiguous; do not create duplicates on timeout.

A checkpoint may include documented uncommitted changes. The skill does not commit or push code solely to prepare a handoff. If a safe checkpoint cannot be reached, it reports the blocker and leaves the transfer pending.

## Runtime requirements

Automatic continuation requires native tools for creating and inspecting conversations, plus successor access to the handoff and workspace. The [Codex desktop reference](skills/handoff/references/codex.md) describes the supported tool flow.

In an environment without conversation-creation tools, the skill produces the handoff document and a ready-to-send continuation prompt. It explicitly reports that automatic continuation has not started. Installation in another agent does not imply native conversation support; this repository does not include dedicated adapters for other agents.

The handoff document stays outside the repository. The skill reports the checkpoint, destination conversation, and observed start or blocker. A successful transfer does not establish that the underlying task is complete.

## Attribution and license

`handoff` is adapted from [Matt Pocock's handoff skill](https://github.com/mattpocock/skills/blob/main/skills/productivity/handoff/SKILL.md), extending it with checkpoint selection and active conversation continuation.

Distributed under the [MIT License](LICENSE), with the upstream copyright notice retained.
