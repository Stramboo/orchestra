# Coding / Fix handoff guide

Codex 使用本指南生成项目内 `.orchestra/NEXT_AGENT.md`。生成文件必须填写当前任务的具体值，不保留模板说明或占位符。

## Required content

### Role

明确这是 TRAE Coding Agent 或 Fix Agent。要求只完成当前任务，不扩大范围。

### Goal and scope

写入已确认的目标、允许修改范围、禁止修改范围和非目标。

### Acceptance

写入可以实际验证的完成标准。

### Repository context

列出必要相关文件、适用的 `AGENTS.md` 或项目规则，并要求保留无关用户改动。

### Checks

只写从仓库脚本、CI、规则或维护文档核实的命令。没有核实的命令写 `NOT_VERIFIED`，不要猜测。

### Git handoff

要求 Agent：

1. 在正式交接前提交全部任务代码；不要求每次保存都 commit。
2. 报告 base commit、head commit、修改文件和准确检查结果。
3. 不 push、merge、deploy 或扩大范围，除非另有授权。
4. 不提交 `.orchestra/`。

### Result file

要求 Agent 完成后覆盖 `.orchestra/RESULT.md`，只使用：

```text
Status: COMPLETED | BLOCKED
Base:
Head:
Changed:
Checks:
Risk:
```

内容保持简短。`BLOCKED` 时只写阻塞事实和需要的决定。
