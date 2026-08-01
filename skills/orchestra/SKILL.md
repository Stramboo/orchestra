---
name: orchestra
description: 显式调用的轻量 AI 开发编排 Skill。用户说使用 $orchestra、让 Codex 安排 TRAE Coding/Review、生成下一位 Agent 指令、判断 Quick/Standard/Major、接入已有或新项目、规划升级或准备 GitHub Draft PR 时使用。自动确认当前任务并生成下一步可复制指令；不构建状态机、任务数据库或安全执行平台。
---

# Orchestra

## 目标

减少用户在 Codex、TRAE Coding Agent 和 TRAE Review Agent 之间反复解释与复制背景的工作。

Orchestra 是协作流程，不是 Agent 安全系统。默认各 Agent 遵守指令；用 Git commit 作为交接边界，不建立运行时状态机、结果 ingest、`seal`、证据数据库或防篡改机制。

## 角色

- 项目所有者：决定目标、范围、合并和部署。
- GPT：整理 Idea、产品选择和 Product Brief。
- Codex：确认任务、必要时做技术规划、自动生成下一位 TRAE Agent 指令、处理异常，并在授权范围内 push 和创建 Draft PR。
- TRAE Coding Agent：实现、检查并提交任务 commit。
- TRAE Review Agent：在独立上下文中只读审查固定 commit range。

## 每次调用

1. 判断当前处于任务确认、Coding、Review、修正还是收尾阶段。
2. 只读取完成下一步所需的仓库信息：适用规则、相关文件、真实检查命令、分支和工作树状态。不要默认浏览整个仓库。
3. 如果目标已明确或用户已经说“确认/批准”，直接继续；不要重复确认。
4. 只有会实质改变结果的歧义才问一个简短问题。不要连续进行多轮选项问答。
5. 内部判断 Quick、Standard 或 Major；只有分级会改变流程时才向用户说明。
6. 自动生成下一位 Agent 的完整指令。不要等用户再次询问“接下来该给谁什么指令”。

## 轻量流程

### 1. 任务开始

用一句话复述目标、范围和完成标准。若用户已确认，立即进入下一步。

- Quick：局部、低风险、容易回滚。生成 Coding 指令；默认不要求独立 Review。
- Standard：普通功能或跨文件修复。生成 Coding 指令，完成后自动生成独立 Review 指令。
- Major：架构、数据、权限、迁移、部署或难回滚工作。先给出紧凑 Technical Plan 并取得所有者批准，再生成 Coding 指令；Review 后由 Codex 做定向验收。

### 2. 自动生成 Coding 指令

使用 [assets/CODING_HANDOFF.md](assets/CODING_HANDOFF.md)，填写：

- 任务目标与非目标；
- 允许和禁止修改的范围；
- 验收标准；
- 仓库中真实存在的检查命令；
- Git 交接要求。

在回复中给出一个完整、可直接复制给 TRAE Coding Agent 的 Markdown 块。外部 Agent 未实际运行时写 `NOT_EXECUTED`，不要假装已执行。

### 3. Coding 完成后自动准备 Review

先核对 Coding 报告与 Git：

- 所有任务改动已有 commit；
- 工作树干净，或无关用户改动已明确隔离；
- base commit、head commit 和检查结果明确。

条件不足时，只生成一条给 Coding Agent 的补充指令。条件满足时，使用 [assets/REVIEW_HANDOFF.md](assets/REVIEW_HANDOFF.md) 自动生成 TRAE Review Agent 指令，不让用户重新描述任务。

### 4. Review 后自动路由

- `APPROVED`：Quick/Standard 生成极简结论；Major 进行定向 Codex 验收。若已有 push 和 Draft PR 权限，验证后执行；合并仍等待项目所有者批准。
- `CHANGES_REQUESTED`：自动生成给原 Coding Agent 的修正指令；修正 commit 后重新生成 Review 指令。
- `REJECTED`：停止并用一句话说明需要项目所有者决定什么。

## 报告

Quick 只写五行：

```text
Changed
Files
Checks
Risk
Git
```

Standard 只写：改动、检查、Review、风险、Git/PR。Major 再补计划和 Codex 验收。不要复制长日志、复述整仓背景或生成日常 report 文件。

## 已有项目、新项目与升级

- 已有项目：读取现有规则和真实命令，从一个明确任务直接开始；不要要求先改造整个仓库。
- 新项目：先由 GPT/所有者确认产品方向，再由 Codex 给出最小技术计划和第一条 Coding 指令。
- 后续升级：视为一个新的 Quick、Standard 或 Major 任务，不建立另一套升级系统。
- 只有用户明确要求时，才复制 [assets/AI_DEVELOPMENT_WORKFLOW.md](assets/AI_DEVELOPMENT_WORKFLOW.md) 到项目根目录。

复杂分级、权限冲突或异常处理时才读取 [references/workflow.md](references/workflow.md)。

## Git 与权限

- 同一 worktree 同时只有一个写入 Agent；不同 Agent 可以顺序接力。
- Coding Agent 在正式交接前提交任务改动；不要求每次保存都 commit。
- Review Agent 只读审查 `base...head`，不修改代码或创建 commit。
- Codex 可在用户已授予的范围内提交、push 任务分支并创建或更新 Draft PR。
- 合并、部署、强制 push、历史重写和远程删除需要项目所有者明确批准。
- 保留无关未提交改动，不清理、不覆盖、不夹带。

## 禁止复杂化

不要为 Orchestra 本身创建：

- 持久化任务状态机；
- runtime/task JSON 数据库；
- `seal`、result ingest、replay 或 cleanup 引擎；
- 防恶意 Agent 或零信任控制面；
- 与当前任务无关的报告、审计日志和模板。

只要下一位 Agent 能拿到准确指令、Git 交接清楚、用户少做一步，流程就达成目标。
