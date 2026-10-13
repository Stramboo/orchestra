---
name: orchestra
description: 显式调用的轻量 AI 开发编排 Skill。用户说使用 $orchestra、继续 Orchestra、让 Codex 安排 TRAE Coding/Review、生成下一位 Agent 指令、接入已有或新项目、规划升级或准备 Draft PR 时使用。Codex 在当前项目的 .orchestra/ 中维护三个 Markdown 交接文件并自动准备下一位 Agent；不构建 CLI、状态机、数据库或安全平台。
---

# Orchestra

## 目标

减少用户在 Codex、TRAE Coding Agent 和 TRAE Review Agent 之间重复解释和复制长提示词的工作。

Orchestra 只维护当前任务的三个本地 Markdown 文件：

```text
.orchestra/
├── TASK.md
├── NEXT_AGENT.md
└── RESULT.md
```

这些文件是可覆盖的本地交接便笺，不是状态机、任务历史或证据数据库。Orchestra 不直接运行 TRAE Agent。

## 角色和分级

- 项目所有者：决定目标、范围、合并和部署。
- GPT：整理 Idea、产品选择和 Product Brief。
- Codex：确认任务、必要时规划、写入交接文件、处理 Agent 结果，并在授权范围内 push 和创建 Draft PR。
- TRAE Coding Agent：实现、检查、提交任务代码，并填写 `RESULT.md`。
- TRAE Review Agent：独立、只读审查固定 commit range，只填写 `RESULT.md`。

Quick 是局部低风险任务，默认可省略独立 Review。Standard 使用 Coding → commit → independent Review。Major 先给出紧凑 Technical Plan 并取得项目所有者批准，Review 后再由 Codex 定向验收。

## 开始任务

1. 确认准确项目根目录。Git 项目使用 `git rev-parse --show-toplevel`；非 Git 项目使用用户明确指定或当前工作目录。
2. 只读取适用规则、相关文件、真实检查命令和 Git 状态，不默认浏览整个仓库。
3. 若任务已明确或用户已确认，直接继续；只有会实质改变结果的歧义才问一个简短问题。
4. 内部判断 Quick、Standard 或 Major；只有分级改变流程时才说明。
5. 在项目根目录创建或覆盖 `.orchestra/TASK.md`、`NEXT_AGENT.md` 和 `RESULT.md`。
6. Git 项目把精确条目 `.orchestra/` 追加到 `git rev-parse --git-path info/exclude` 返回的文件；已有该条目时不重复。不修改项目 `.gitignore`。

### 写入 `TASK.md`

使用简洁 Markdown，填写具体内容：

```markdown
# Task

Current step: CODING

## Goal

## Scope

## Non-goals

## Acceptance

## Relevant files

## Git boundary
```

`Current step` 只允许 `CODING`、`REVIEW`、`FIX` 或 `DONE`，仅供人阅读。不要验证转换，不保存历史。

### 写入 Coding 指令

根据 [assets/CODING_HANDOFF.md](assets/CODING_HANDOFF.md) 将完整、无占位符的指令写入 `NEXT_AGENT.md`，包含目标、范围、非目标、验收、相关文件、已核实命令、无关改动保护、commit 交接和填写 `RESULT.md` 的要求。

初始化 `RESULT.md`：

```text
Status: COMPLETED | BLOCKED
Base:
Head:
Changed:
Checks:
Risk:
```

聊天中只报告下一位 Agent 类型、`NEXT_AGENT.md` 绝对路径，并告诉用户可以开始执行。正常情况下不要重复粘贴文件中的完整指令。只有项目目录不可写时，才在聊天中输出完整 Markdown 指令。

## 继续任务

当用户再次调用 `$orchestra` 或说“继续 Orchestra”时，只读取：

- `.orchestra/TASK.md`；
- `.orchestra/RESULT.md`；
- 当前 Git status；
- 报告的 base/head commit；
- 做决定所需的最小 diff 和检查结果。

然后自动完成下一步：

### Coding 或 Fix 结果

- `BLOCKED`：简短告诉用户需要决定的问题，不生成额外系统。
- `COMPLETED` 但任务代码未 commit、base/head 不明确或交接范围不清：保持当前步骤，覆盖 `NEXT_AGENT.md` 为 Coding 补充指令，并重新初始化 Coding 结果格式。
- Quick 且证据充分：把 `TASK.md` 的 `Current step` 改为 `DONE`，在 `NEXT_AGENT.md` 写明无需下一位 Agent，输出五行结论。
- Standard/Major 且证据充分：把当前步骤改为 `REVIEW`；根据 [assets/REVIEW_HANDOFF.md](assets/REVIEW_HANDOFF.md) 写入完整 Review 指令；将 `RESULT.md` 覆盖为 Review 结果格式。

Review 结果格式：

```text
Status: APPROVED | CHANGES_REQUESTED | REJECTED
Blocking findings:
Checks:
Residual risk:
```

### Review 结果

- `CHANGES_REQUESTED`：把当前步骤改为 `FIX`；将问题和原任务范围写入 Coding/Fix 指令；重新初始化 Coding 结果格式。
- `APPROVED`：把当前步骤改为 `DONE`；在 `NEXT_AGENT.md` 写明无需下一位 Agent；Major 再做定向 Codex 验收。已有授权时可以验证后 push 并创建或更新 Draft PR。
- `REJECTED`：保持文件供用户查看，简洁说明需要项目所有者决定什么。

Review Agent 禁止修改源码或创建 commit，只允许写 `.orchestra/RESULT.md`。

## Git 与报告

- 同一 worktree 同时只有一个源码写入 Agent；不同 Agent 可以顺序接力。
- Coding/Fix Agent 正式交接前提交任务代码；不要求每次保存都 commit。
- Review Agent 只审查明确的 `git diff <base>...<head>`。
- `.orchestra/` 必须保持在 Git 本地 exclude 中，不进入任务 commit。
- 保留无关用户改动，不清理、不覆盖、不夹带。
- Codex 可在用户已授权范围内提交、push 和创建或更新 Draft PR。
- 合并、部署、强制 push、历史重写和远程删除需要项目所有者明确批准。

Quick 最终报告只写：Changed、Files、Checks、Risk、Git。Standard 只补 Review 和 PR 状态。不要创建日常 report 或复制长日志。

## 已有项目、新项目与升级

- 已有项目从一个明确任务直接开始，不要求先改造仓库。
- 新项目先确认产品方向，再由 Codex给出最小技术计划和第一条 Coding 指令。
- 后续升级仍作为一个 Quick、Standard 或 Major 任务处理。
- 只有用户明确要求时，才复制 [assets/AI_DEVELOPMENT_WORKFLOW.md](assets/AI_DEVELOPMENT_WORKFLOW.md) 到项目根目录。
- 复杂分级、权限冲突或异常处理时才读取 [references/workflow.md](references/workflow.md)。

## 禁止复杂化

不要增加 CLI、运行时程序、JSON、Schema、持久化状态机、任务历史、数据库、`bootstrap`、`advance`、`seal`、`ingest`、`replay`、cleanup/retention engine、凭据扫描、防篡改控制、Specialist 路由、多任务管理或独立 Orchestra Pro。

只要三个文件让用户少复制一次、让下一位 Agent 获得准确指令、让 Git 交接清楚，流程就完成目标。
