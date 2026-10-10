# Agent Skills

可按需安装的 Agent 技能。目前包含 `handoff`：先把任务推进到可恢复的交接节点，再生成交接文档，主动创建新会话继续执行。

## 统一安装

使用 [skills CLI](https://github.com/vercel-labs/skills)，全局安装到 Codex：

```sh
npx skills@latest add LAwLi3tCoding/agent-skills --skill handoff --agent codex --global --yes
```

其他兼容 Agent 可使用同一个仓库入口，交互选择技能和目标 Agent：

```sh
npx skills@latest add LAwLi3tCoding/agent-skills
```

本仓库面向 Codex，未提供其他 Agent 的专用配置。自动创建新会话需要宿主提供会话工具；仅支持读取技能的环境会输出交接文档和续接提示词，并明确说明尚未启动。

## 使用

```text
$handoff 下一轮继续完成剩余实现，保留当前验收要求。
```

技能会检查正在执行的工具、后台进程和协作任务。只完成到可交接节点所必需的最小步骤，不要求完成整个任务，不为交接自动提交或推送。创建新会话前，原会话先停止写入；启动后由新会话接管执行，原会话仅观察接管结果，避免重复操作。

交接文档保存在系统临时目录，不进入代码仓库。它记录任务目标、已有授权、代码与产物状态、检查证据、不确定结果和下一步。已有计划等材料只引用路径或链接，敏感内容会被隐藏。

创建新会话成功不等于任务完成。技能会分别报告交接节点、创建结果，以及是否观察到新会话开始执行；无法确定的外部操作优先核对结果，不盲目重试。

安装后在下一轮对话中使用。更新已安装技能可运行 `npx skills@latest update`。

## 来源与许可

基于 [Matt Pocock 的 handoff](https://github.com/mattpocock/skills/blob/main/skills/productivity/handoff/SKILL.md) 改写，增加交接节点判断和新会话续接流程。保留上游 MIT 许可，见 [LICENSE](LICENSE)。
