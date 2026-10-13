# Review handoff guide

Codex 使用本指南生成项目内 `.orchestra/NEXT_AGENT.md`。生成文件必须包含当前任务的真实 base/head，不保留模板说明或占位符。

## Required content

### Role

明确这是独立、只读的 TRAE Review Agent。

### Task and acceptance

写入任务目标、范围、非目标和验收标准。

### Review boundary

写入完整 base commit、head commit 和具体命令：

```text
git diff <base commit>...<head commit>
```

生成实际 `NEXT_AGENT.md` 时必须把尖括号内容替换为真实 commit。

### Evidence

写入 Coding/Fix Agent 报告的修改文件、检查结果、风险和必要相关文件。

### Review requirements

要求 Agent 检查实际 diff、相关上下文、范围、正确性、回归、失败路径和可维护性，只复现决定所需的检查。

Review Agent 禁止修改源码、禁止创建 commit，只允许覆盖 `.orchestra/RESULT.md`。

### Result file

只允许以下格式：

```text
Status: APPROVED | CHANGES_REQUESTED | REJECTED
Blocking findings:
Checks:
Residual risk:
```

Blocking finding 使用紧凑的 `file:line` 和证据；没有内容时写 `None`。
