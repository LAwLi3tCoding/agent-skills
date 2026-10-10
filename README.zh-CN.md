# Agent Skills

[English](README.md) · **简体中文**

面向编程 Agent 的可复用技能，重点解决任务的可靠续接。每个技能都可独立安装。目前面向 Codex，并提供 Codex 桌面应用的原生会话续接流程。

## 可用技能

| 技能 | 用途 |
| --- | --- |
| [handoff](skills/handoff/SKILL.md) | 将进行中的任务推进到可恢复的节点，生成交接文档，并创建新会话继续执行。 |

## 安装

使用 [skills CLI](https://github.com/vercel-labs/skills)，为 Codex 全局安装 `handoff`：

```sh
npx skills@latest add LAwLi3tCoding/agent-skills --skill handoff --agent codex --global --yes
```

安装后可在下一轮对话中使用。

如需交互选择技能和支持的 Agent：

```sh
npx skills@latest add LAwLi3tCoding/agent-skills
```

对于通过 skills CLI 管理的安装，可使用以下命令更新 `handoff`：

```sh
npx skills@latest update handoff --global --yes
```

## 使用

请求交接时，说明下一次会话需要关注的内容：

```text
$handoff 在新会话中继续完成剩余实现，保留现有验收要求。
```

该请求授权新会话在现有任务范围内继续执行。待审批事项、只读限制及其他授权边界会随交接保留。

## 交接流程

1. **到达可恢复节点。** 检查正在执行的工具、后台进程和协作任务，只完成使任务状态明确所必需的最小步骤，不要求完成整个项目。外部操作结果不确定时，先核对结果，再决定是否重试。
2. **准备交接材料。** 在工作区外的私有临时位置写入简洁文档，记录目标、决策、产物状态、验证证据、未决问题和下一步。已有材料使用引用，隐藏密钥及不必要的个人信息。
3. **启动续接会话。** 创建新会话前，原会话先停止写入。接手会话读取交接材料、核实当前状态，并在原有授权范围内继续执行。原会话观察接管结果，不再修改共享产物。
4. **确认交接结果。** 区分会话创建成功和已观察到开始执行。初始化尚未完成或结果不明确时，继续检查已有会话，不因超时重复创建。

交接节点可以包含已记录清楚的未提交改动。技能不会仅为交接而提交或推送代码。无法到达安全节点时，会说明阻塞原因，并保持交接待完成。

## 运行条件

自动续接需要宿主提供创建和检查会话的原生工具，接手会话也需要能够访问交接文档和工作区。具体工具流程见 [Codex 桌面应用参考](skills/handoff/references/codex.md)。

缺少会话创建工具时，技能会生成交接文档和可直接发送的续接提示词，并明确报告尚未自动启动续接。在其他 Agent 中安装技能，不代表该环境具备原生会话支持；本仓库未提供其他 Agent 的专用适配器。

交接文档不会进入代码仓库。技能会报告交接节点、目标会话，以及已观察到的启动结果或阻塞原因。交接成功不代表原任务已经完成。

## 来源与许可

`handoff` 基于 [Matt Pocock 的 handoff 技能](https://github.com/mattpocock/skills/blob/main/skills/productivity/handoff/SKILL.md) 改写，增加交接节点判断和主动创建续接会话的流程。

采用 [MIT 许可](LICENSE)，保留上游版权声明。
