# DeepSeek Harness (DSH)

DSH's filesystem skill provider reads `<project>/.agents/skills` and `<project>/.dsh/skills`, plus global roots. The shared global root defaults to `~/.agents/skills` (`DSH_AGENTS_HOME` or provider configuration can override it); the DSH root defaults to `~/.dsh/skills`. Install the whole `handoff` folder, including its references. Base-backed profiles include skill discovery; a custom profile must expose both its skill provider and loading tool.

Load `handoff` through the registered `skill` tool or the host's skill UI. Do not assume `/handoff` is available in every DSH surface. `agents/openai.yaml` is optional Codex metadata.

## Choose a supported session interface

Prefer a native top-level session interface if the current DSH host exposes one. Use its real registered schema to create a root session, submit the continuation prompt, and observe durable events. Do not invent a `create_thread` tool or use `subagent` as a substitute.

For CLI continuation, inspect `dsh --profile headless --help` first. Only use the following when the installed profile supports `--json` and its effective model, reasoning effort, preset, workspace, permission policy, and required tools match the source:

```sh
dsh --profile headless --json "$continuation_prompt"
```

Run from the original workspace in a durable host-managed process. Omit `--session-id` on the first launch: the flag adopts an existing persisted session, and does not allocate a new identity. Record the opening `session` event's `sessionId`. A subsequent `status` event with `phase: turn_start` proves a turn began; verify actual handoff-read and next-action evidence through `tool_call`/`tool_result` and assistant events. A `final` event can accompany failure, so also check `turn_end` reason and process exit.

Older headless versions may print only a final answer without an identity-bearing stream. Do not assume support from the latest documentation or treat an opaque process as a confirmed transfer. Inspect the same persisted session through an available authorized host interface, or report that start cannot be verified.

An already-available DSH SDK is another option when equivalent configuration is established. Its documented JSON-RPC `initialize` establishes workspace/model context; `session/prompt` submits `contentBlocks` under a fresh `sessionId`. A `{messageId}` response acknowledges inbox admission, not execution. Observe the matching `session.event` and `session.status` before claiming that the handoff was read and work began. Use the installed SDK schema rather than constructing raw requests from this summary.

Switching a Web/desktop/custom profile to `headless` or `sdk` does not itself preserve its configuration. Do not install plugins, migrate profiles, change permission environment variables, or dump secret-bearing config to make a handoff work. If equivalent configuration or a durable launcher is unavailable, return the document and prompt with the specific dependency. Reconcile pending operations using the captured session/process identity; never repeat creation after an uncertain result.

Sources: [Skill discovery](https://github.com/deepseek-ai/deepseek-harness/blob/master/packages/skill/skill-filesystem/README.md), [Headless sessions](https://github.com/deepseek-ai/deepseek-harness/blob/master/packages/bundle/headless/README.md), [SDK](https://github.com/deepseek-ai/deepseek-harness/blob/master/python/sdk/README.md).
