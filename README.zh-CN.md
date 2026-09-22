# decompose-large-tasks

一个面向 Codex 的大型任务拆解 Skill：把模糊、跨文件、跨系统或跨阶段的目标，整理成依赖明确、可以独立验证、能够安全交接的小任务计划。

[English README](README.md)

## 它解决什么问题

大型任务最容易出现的不是“不会写代码”，而是：目标没有冻结就开始实现、任务之间依赖不清、风险到最后才暴露、测试通过却没有验证真实用户路径，以及多个 Agent 修改同一边界。

这个 Skill 在规划阶段先检查事实和准备度，再输出可执行的任务合同。它默认只生成计划，不会自动改代码、提交 Git、创建 Issue、启动子 Agent 或修改外部系统。

## 核心能力

- 读取用户需求、`AGENTS.md`、相关代码、现有计划和仓库约定，再决定如何拆分。
- 在目标、范围、架构或验收证据不足时返回阻塞状态，不用猜测填满任务清单。
- 为需求和约束分配 `R1`、`R2` 等稳定 ID，并把每项需求绑定到唯一的主任务和验收证据。
- 把高风险未知数单独变成证据任务，先验证会推翻方案的事实。
- 使用纵向切片，让每个任务尽量交付一条可观察的完整行为，而不是按数据库、API、前端做水平堆叠。
- 为每个任务写清目标、依赖、文件、接口、步骤、验收、验证、证据、回滚边界和明确排除项。
- 把无硬依赖的任务分成安全并行波次，并在分支汇合处设置集成检查点。
- 区分 `IMPLEMENTED`、`ENABLED`、`EXECUTED`、`VERIFIED` 和 `COMPLETE`，避免把“代码存在”当成“真实目标已完成”。
- 对迁移、替换、权限、费用、数据删除和 Agent 控制流补充停止条件、恢复位置和 Legacy 退出条件。

## 安装

### PowerShell

```powershell
git clone https://github.com/shitiwen/decompose-large-tasks.git "$HOME\.codex\skills\decompose-large-tasks"
```

### macOS / Linux

```bash
git clone https://github.com/shitiwen/decompose-large-tasks.git ~/.codex/skills/decompose-large-tasks
```

安装后新开一个 Codex task。如果没有立即发现 Skill，重启 Codex。

## 使用

显式调用：

```text
使用 $decompose-large-tasks，把这个大任务拆成详细、可验证的小任务，只规划不实施：
<目标、约束、已有文档、代码路径或相关链接>
```

也可以直接描述任务；Skill 的 description 支持自动触发。为了避免误操作，默认输出模式是只在当前回复中给出计划。

支持三种输出模式：

- `response-only`：只在当前回复中输出完整计划，默认模式。
- `file`：保存到用户指定或仓库已有约定的计划文件。
- `tracker`：只有用户明确要求时，才把任务写入外部 Issue、项目管理器或其他追踪系统。

## 计划会包含什么

完整计划通常包含：

1. 目标、范围、排除项、约束和成功证据。
2. 当前系统的真实入口、责任归属、数据流和已有模式。
3. 准备度状态、假设和需要先验证的未知数。
4. 需求到任务的追踪表。
5. 方案、接口和迁移时的职责转移表。
6. 依赖图、关键路径和安全并行波次。
7. 每个任务的可执行合同和独立验收条件。
8. 集成检查点、真实用户旅程和禁止替代路径。
9. 风险、停止动作、恢复位置和最终覆盖检查。

输出模板见 [`references/plan-format.md`](references/plan-format.md)。任务边界、迁移、并行、验收和反臃肿规则见 [`references/decomposition-rules.md`](references/decomposition-rules.md)。

## 什么时候不该用

以下情况通常不需要它：

- 明确的一文件小改动；
- 一眼就能完成的配置或文案修改；
- 只需要解释概念、做代码审查或回答事实问题；
- 用户已经提供了完整、可执行且经过验证的任务清单。

## 仓库结构

```text
.
├── SKILL.md
├── README.md
├── README.zh-CN.md
├── LICENSE
├── agents/openai.yaml
└── references/
    ├── plan-format.md
    └── decomposition-rules.md
```

## 许可证

MIT，详见 [`LICENSE`](LICENSE)。

