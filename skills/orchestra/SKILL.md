---
name: orchestra
description: 为已有或新项目启用并运行个人 AI 多 Agent 开发流程，按风险划分快速、标准和重大任务，生成 GPT、Codex、TRAE 交接材料、项目 AGENTS.md、Codex 最小审核上下文和升级计划。仅在用户显式调用 $orchestra 以启用个人开发流程、任务分级、Agent 交接、里程碑或升级规划时使用；普通编码任务不应触发。
---

# Orchestra

## 目标

把项目所有者、GPT、Codex、TRAE Coding Agent 与 TRAE Review Agent 串成可验证、低 Token 的顺序流程。保留项目所有者对产品、合并和部署的最终决定权，只在重大、里程碑或异常情形使用 Codex。

## 入口模式

每次调用只选择一个主要模式。根据用户动词和当前项目状态选择：

- `BOOTSTRAP_EXISTING`：为已有仓库采集真实证据并准备采用流程。
- `BOOTSTRAP_NEW`：为尚未开始的项目准备最小目录规则和首个产品交接。
- `CLASSIFY_TASK`：把一个具体任务判定为 Quick、Standard 或 Major。
- `CREATE_HANDOFF`：根据已批准内容生成下一角色可直接执行的交接材料。
- `PLAN_UPGRADE`：把后续升级纳入同一分级、计划、审查和验收循环。
- `PREPARE_CODEX_REVIEW`：只整理触发 Codex 所需的最小证据包。
- `UPDATE_PROJECT_RULES`：根据仓库真实情况创建或更新项目规则提案。

如果请求同时包含多个动作，选择决定后续工作的动作作为主要模式，并把其余动作列为后续步骤。不要隐式扩大修改范围。

## 项目检查

在生成或修改项目文件前：

1. 确认准确项目根目录和 Git 仓库状态。
2. 查找并完整读取适用的 `AGENTS.md`、现有工作流、计划、README、脚本和 CI 配置。
3. 记录当前分支、工作树、未提交改动和相关文件。
4. 从仓库证据确定真实运行、测试、lint、typecheck 和 build 命令；不得发明命令。
5. 标明已验证事实、推断和未知项。

除非用户明确要求采用或更新流程，不修改项目文件。已有 `AGENTS.md` 时不得覆盖：先检查内容，提出最小合并方案，只改与本流程直接相关的部分。

## 资源读取

按主要模式只读取必要资源：

- 对 `BOOTSTRAP_EXISTING`、`BOOTSTRAP_NEW`、`UPDATE_PROJECT_RULES`、日常路由或 `PLAN_UPGRADE`，读取 `references/adoption.md`。
- 对分级冲突、Major 工作、权限、Git/GitHub、失败处理或 Codex 验收，读取 `references/workflow.md`。
- 已能从本文件和现有项目规则完成请求时，不读取额外 reference。

模板位于 `assets/`：

- 项目规则：`assets/AI_DEVELOPMENT_WORKFLOW.md`、`assets/AGENTS.md`
- Quick：`assets/tasks/QUICK_HANDOFF.md`
- 产品阶段：`assets/tasks/PRODUCT_BRIEF.md`
- 技术规划：`assets/tasks/TECHNICAL_PLAN.md`
- Standard：`assets/tasks/STANDARD_TASK.md`
- 高风险回滚：`assets/tasks/ROLLBACK_PLAN.md`
- Codex 验收：`assets/tasks/CODEX_ACCEPTANCE.md`

复制模板并用当前项目证据填写，不重新创作同类格式。删除不适用的占位命令行，不保留虚假示例。

## 执行规则

### BOOTSTRAP_EXISTING

检查现有仓库与规则，输出采用差距、建议写入的文件和首个任务路由。只有用户授权写入时才复制并定制项目规则。

### BOOTSTRAP_NEW

先生成 Product Brief 交给项目所有者批准。产品方向批准后再生成 Major Technical Plan；不要在产品未定时编造代码结构。

### CLASSIFY_TASK

使用影响范围、风险、回滚难度和验证成本分级。Quick 跳过 Codex；Standard 复用已批准计划并由两个独立 TRAE 角色完成实现与审查；Major 先由 Codex 规划，并在触发点定向验收。无法确定时选择较高等级并说明原因。

### CREATE_HANDOFF

选择与任务等级和下一角色匹配的模板。只加入目标、边界、验收、真实命令、必要文件、风险和 Git 状态。外部 GPT 或 TRAE 未在当前环境实际运行时标记 `NOT_EXECUTED`。

### PLAN_UPGRADE

先核对当前版本、运行状态、已知债务和升级目标，再分级。Major 升级必须包含批准的产品依据、Technical Plan、回滚方案和验收触发条件。

### PREPARE_CODEX_REVIEW

只收集批准计划、相关 diff、必要文件、检查结果、最小失败日志、未决问题与 Git/GitHub 状态。不要让 Codex 为普通改动重新浏览整个仓库。

### UPDATE_PROJECT_RULES

以模板为基线，根据真实仓库证据做最小定制。保留项目既有有效规则，指出冲突，未经项目所有者决定不静默覆盖。

## Token 与安全

- Quick 报告严格保持五行。
- Standard 报告只保留任务卡、实现结果、独立审查、风险和 Git 状态。
- 重大任务只在必要阶段读取完整工作流或较大证据。
- 不复制大段日志、整仓摘要或隐藏推理。
- 保留无关未提交改动，不清理、不覆盖、不夹带。
- 不在提示、模板、报告、源码或提交中放置凭据、令牌、个人联系信息或支付信息。
- Codex 可以在任务范围内提交、推送任务分支并创建或更新 PR；合并、部署、强制推送、历史重写和远程删除遵守项目所有者授权边界。

## 输出

最终只报告：

- 创建或更新的产物绝对路径；
- 已验证事实和实际运行的检查；
- 未解决问题或 `NOT_EXECUTED` 项；
- 分支、提交、工作树、推送和 PR 的真实状态；
- 下一位负责角色及其所需动作。

不要宣称未实际执行的 Agent、检查、推送、PR、合并或部署已经完成。
