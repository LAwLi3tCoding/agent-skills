# Agent Skills

[English](README.md) · **简体中文**

面向编程 Agent 的可复用技能，重点解决任务的可靠续接。每个技能都可独立安装。同一份技能指令支持 **Codex、Claude Code（CC）、OpenCode 和 DeepSeek Harness（DSH）**，各平台的会话启动方式通过独立参考文件说明。

## 可用技能

| 技能 | 用途 |
| --- | --- |
| [handoff](skills/handoff/SKILL.md) | 将进行中的任务推进到可恢复的节点，生成交接文档，并创建新会话继续执行。 |

## 安装

使用 [skills CLI](https://github.com/vercel-labs/skills)，为 Codex、Claude Code 和 OpenCode 全局安装 `handoff`：

```sh
npx skills@latest add LAwLi3tCoding/agent-skills --skill handoff --agent codex claude-code opencode --global --yes
```

启用文件系统技能提供器的 DSH 也能发现该安装创建的共享目录 `~/.agents/skills/handoff`。如果设置了 `DSH_AGENTS_HOME`，应使用配置后的根目录。高优先级目录中的同名技能可能覆盖共享安装，应更新实际加载的副本，避免保留不同版本。安装不会修改模型、profile、权限或 hooks。

如果宿主尚未刷新技能目录，可在新一轮或新会话中加载。

如需交互选择技能和支持的 Agent：

```sh
npx skills@latest add LAwLi3tCoding/agent-skills
```

对于通过 skills CLI 管理的安装，可使用以下命令更新 `handoff`：

```sh
npx skills@latest update handoff --global --yes
```

## 使用

请求交接时，说明下一次会话需要关注的内容。Codex 示例：

```text
$handoff 在新会话中继续完成剩余实现，保留现有验收要求。
```

Claude Code 中可使用 `/handoff` 并附上同样的任务说明。OpenCode 和 DSH 中可直接要求 Agent 加载 `handoff` 技能，并在新会话中继续任务；界面快捷入口以宿主实际提供的能力为准。

仅生成续接提示词：

```text
$handoff --prompt-only 保留现有验收要求，继续完成剩余实现。
```

Claude Code 使用 `/handoff --prompt-only ...`；OpenCode 和 DSH 可在加载技能时明确要求相同模式。此模式准备交接文档及可直接发送的提示词，停止任务执行并结束当前回合，不启动接手会话，也不关闭当前会话。不带该参数时，默认创建续接会话。

技能仅响应用户显式请求。Claude Code 使用 `disable-model-invocation`，Codex 使用 `allow_implicit_invocation: false` 提供宿主级调用限制；其他宿主没有等价控制时，通过技能指令保留同样的要求。

该请求授权新会话在现有任务范围内继续执行。待审批事项、只读限制及其他授权边界会随交接保留。

## 交接流程

1. **到达可恢复节点。** 检查正在执行的工具、后台进程和协作任务，只完成使任务状态明确所必需的最小步骤，不要求完成整个项目。外部操作结果不确定时，先核对结果，再决定是否重试。
2. **准备交接材料。** 在工作区外的私有临时位置写入简洁文档，记录目标、决策、产物状态、验证证据、未决问题和下一步。已有材料使用引用，隐藏密钥及不必要的个人信息。
3. **启动续接会话（默认模式）。** 创建新会话前，原会话先停止写入。接手会话读取交接材料、核实当前状态，并在原有授权范围内继续执行。原会话观察接管结果，不再修改共享产物。
4. **确认交接结果（默认模式）。** 区分会话创建成功和已观察到开始执行。初始化尚未完成或结果不明确时，继续检查已有会话，不因超时重复创建。

交接节点可以包含已记录清楚的未提交改动。技能不会仅为交接而提交或推送代码。无法到达安全节点时，会说明阻塞原因，并保持交接待完成。

## 运行条件

四个平台共用相同的交接节点和执行权转移规则。自动续接需要可使用的会话接口、交接文档及工作区访问权限，以及保留原会话有效配置的能力。

| 平台 | 续接方式 | 参考文件 |
| --- | --- | --- |
| Codex | 桌面应用会话工具，或持久化的 `codex exec` 会话 | [Codex](skills/handoff/references/codex.md) |
| Claude Code（CC） | 原生后台会话，或受管理的持久化 CLI 会话 | [Claude Code](skills/handoff/references/claude-code.md) |
| OpenCode | 会话 API，或受管理的 `opencode run` 会话 | [OpenCode](skills/handoff/references/opencode.md) |
| DeepSeek Harness（DSH） | 宿主会话接口、配置兼容的 headless profile，或已有 SDK | [DSH](skills/handoff/references/dsh.md) |

技能会先检查已安装版本的能力，再选择启动方式。受管理的 CLI 会话也是独立、持久化的新会话，不一定打开新界面标签页。CLI 进程需要能够持续运行的宿主管理机制。DSH 切换 profile 前，需要确认模型、工具和权限配置等价。技能不会用子 Agent 代替新会话，也不会绕过审批门禁。

没有适用的启动接口时，技能会生成交接文档和可直接发送的续接提示词，并报告尚未自动启动。`agents/openai.yaml` 仅提供可选的 Codex 界面信息，其他宿主直接使用 `SKILL.md` 及其参考文件。

交接文档不会进入代码仓库。技能会报告交接节点、目标会话，以及已观察到的启动结果或阻塞原因。交接成功不代表原任务已经完成。

## 来源与许可

`handoff` 基于 [Matt Pocock 的 handoff 技能](https://github.com/mattpocock/skills/blob/main/skills/productivity/handoff/SKILL.md) 改写，增加交接节点判断和主动创建续接会话的流程。

采用 [MIT 许可](LICENSE)，保留上游版权声明。
