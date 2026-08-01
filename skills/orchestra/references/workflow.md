# Orchestra 轻量工作流

- Version: 1.0.2
- Authority: Project owner

只在分级冲突、Major 任务、异常或 Git/GitHub 权限不清楚时读取本文。

## 流程

```text
用户确认任务
→ Codex 生成 Coding 指令
→ TRAE Coding 实现、检查、commit
→ Codex 生成 Review 指令（Standard/Major）
→ TRAE Review 只读审查 base...head
→ 修正或通过
→ Codex 极简验收、push、Draft PR
→ 用户决定合并与部署
```

Quick 默认省略独立 Review。Major 在 Coding 前增加 Codex Technical Plan，在 Review 后增加定向 Codex 验收。

## 分级

| Level | 使用场景 | 流程 |
|---|---|---|
| Quick | 文档、文案、局部低风险修复 | Coding → checks → compact report |
| Standard | 普通功能、行为变化、跨文件修复 | Coding → commit → independent Review |
| Major | 架构、数据、权限、迁移、部署、难回滚变更 | Codex plan → owner approval → Coding → Review → Codex acceptance |

无法判断时先按较高一级处理，但只说明原因，不启动额外系统。

## Git 交接

Coding Agent 在交接时提供：

```text
Base commit
Head commit
Changed files
Checks and results
Worktree status
```

工作树不干净或任务改动未提交时标记 `NOT_READY`。Codex 不替 Coding Agent 猜测 review range。Review Agent 只读，不提交修复；问题退回原 Coding Agent 创建新 commit。

## 异常

- 范围扩大、检查失败或仓库事实与任务冲突：停止并交给 Codex。
- 产品目标不清：交给项目所有者/GPT。
- Review 为 `CHANGES_REQUESTED`：自动生成修正指令并复审。
- Review 为 `REJECTED`：停止，等待项目所有者决定。
- 外部 Agent 不可用：只生成指令并标记 `NOT_EXECUTED`。

## 权限

- Codex 可以在已授权范围内 push 任务分支并创建或更新 Draft PR。
- 项目所有者决定合并与部署。
- 强制 push、历史重写、远程删除和破坏性操作需要单独批准。

## Token 原则

- 不重读未变化模块。
- 不复制完整聊天和长日志。
- Quick 只用五行报告。
- Standard 只提供任务、diff、检查、Review 和 Git 状态。
- Codex 只在 Major、异常、冲突、里程碑或用户明确要求时扩大检查。
