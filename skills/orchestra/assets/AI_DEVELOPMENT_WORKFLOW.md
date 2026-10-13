# AI Development Workflow

- Version: 1.0.2
- Authority: Project owner

## Roles

- GPT：整理 Idea、产品方案和 Product Brief。
- Codex：确认任务、规划 Major 工作、生成下一位 TRAE Agent 指令、处理异常，并在授权范围内 push/Draft PR。
- TRAE Coding Agent：实现、检查并提交 commit。
- TRAE Review Agent：独立、只读审查固定 commit range。
- Project owner：决定范围、合并和部署。

## Flow

```text
Confirm task
→ Codex writes .orchestra/TASK.md, NEXT_AGENT.md, RESULT.md
→ Coding instruction in NEXT_AGENT.md
→ Coding Agent implementation + checks + commit
→ Coding Agent writes RESULT.md
→ User continues Orchestra
→ Review instruction in NEXT_AGENT.md (Standard/Major)
→ Review Agent verdict
→ Fix or accept
→ Codex push/Draft PR when authorized
→ Owner merge/deploy decision
```

Quick 默认省略独立 Review。Major 在实现前需要 Codex Technical Plan 和所有者批准，在 Review 后需要 Codex 定向验收。

## Handoff

- `.orchestra/`只保存当前任务的本地 Markdown 交接文件，并加入本地 Git exclude。
- 同一 worktree 同时只有一个写入 Agent。
- Coding Agent 正式交接前提交全部任务改动并报告 base/head、检查结果和工作树状态。
- Coding/Fix Agent 填写 `.orchestra/RESULT.md`。
- Review Agent 不修改代码或创建 commit，只填写 `.orchestra/RESULT.md`；问题退回 Coding Agent。
- 不要求每次保存都 commit。

## Reports

Quick 只写：Changed、Files、Checks、Risk、Git。

Standard 只写：改动、检查、Review、风险、Git/PR。

## Authority

- Codex 可以在已授权范围内 push 任务分支并创建或更新 Draft PR。
- 合并、部署、强制 push、历史重写和远程删除需要项目所有者批准。
- 保留并隔离无关用户改动。

Orchestra 不直接运行 TRAE Agent，只编排三个本地 Markdown 交接文件；不建立状态机、任务数据库、结果 ingest 或防恶意 Agent 系统。
