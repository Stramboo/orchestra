# AI Development Workflow

- Workflow version: 1.0.0
- Status: Active
- Authority: Project owner

## 1. Sources of truth

按以下优先级执行：

1. 项目所有者当前明确指令。
2. 已批准的 Product Brief、Technical Plan 与变更范围。
3. 项目根目录 `AGENTS.md`。
4. 本文档。
5. 仓库现有代码、测试、配置与文档所证明的事实。

遇到冲突时停止扩大实现范围，记录冲突并交由项目所有者决定。不得用 Agent 的推断替代产品决策。

## 2. Role routing

- 项目所有者：决定产品方向、范围、合并与部署。
- GPT：形成项目大纲、创意方案和 Product Brief；不直接决定技术实现。
- Codex：负责重大任务、里程碑和升级的技术规划，以及被触发时的定向验收。
- TRAE Coding Agent：按已批准计划编写代码并运行规定检查。
- TRAE Review Agent：独立复核实现、验收标准和风险，不兼任同一任务的 Coding Agent。

同一工作树可以顺序交接给不同 Agent，以验证代码的可读性与连续性；同一时刻只允许一个写入者。

## 3. Protect Codex budget

Codex 只在以下情况介入：

- 重大任务或新里程碑开始；
- 架构、数据、安全、隐私、权限或部署边界变化；
- 实现偏离已批准计划；
- TRAE 审查无法解决的争议或失败；
- 项目所有者明确要求；
- 最终验收需要独立技术判断。

普通小改动和已规划的标准任务不重复让 Codex 浏览整个仓库。提供给 Codex 的上下文应只包含计划、相关 diff、必要文件、测试结果、失败日志和未决问题。

## 4. Task levels

### Quick

适用于局部、低风险、容易回滚且不改变公共接口或数据结构的改动。

- 跳过 Codex。
- TRAE Coding Agent 实现并执行最小相关检查。
- 报告严格使用五行：Changed、Files、Checks、Risk、Git。
- 出现范围扩大、检查失败或安全影响时升级为 Standard 或 Major。

### Standard

适用于范围清晰、可复用已有计划、影响有限但需要独立审查的工作。

- 复用已批准的 Product Brief 或 Technical Plan。
- TRAE Coding Agent 实现。
- TRAE Review Agent 独立审查。
- 只有命中 Codex 触发条件才升级给 Codex。

### Major

适用于新项目、新里程碑、架构变化、迁移、权限与安全变化、高风险升级或难以回滚的工作。

- 先由项目所有者批准产品方向。
- Codex 根据真实仓库证据制定 Technical Plan、验收标准和回滚方案。
- TRAE Coding Agent 实现，TRAE Review Agent 独立审查。
- Codex 在触发点执行定向验收，项目所有者决定合并与部署。

无法确定等级时选择较高一级，并说明原因。

## 5. Independent TRAE review

Review Agent 必须基于验收标准、相关 diff、检查结果和必要代码独立判断。审查状态只能是：

- `APPROVED`
- `CHANGES_REQUESTED`
- `REJECTED`

没有发现或残余风险时写 `None`，不得省略。`CHANGES_REQUESTED` 返回 Coding Agent 修复并重新审查；`REJECTED` 交给项目所有者决定是否重做、改计划或终止。

## 6. Git, GitHub, and worktrees

- 每个任务使用独立分支；Codex 创建的分支默认使用 `codex/` 前缀。
- 开始前记录当前分支、工作树状态和已有未提交改动。
- 不覆盖、清理或夹带与任务无关的用户改动。
- Codex 可在任务范围内提交、推送任务分支，并创建或更新 GitHub Pull Request。
- 合并和部署始终需要项目所有者批准。
- 强制推送、重写历史、删除远程分支或其他难恢复操作需要单独明确授权。
- 需要并行写入时使用不同 worktree；顺序交接可以使用同一 worktree，但必须确认前一 Agent 已停止写入并留下清晰 Git 状态。

## 7. Compact reports

报告只保留决策所需信息：

- Quick：固定五行，不写长篇过程。
- Standard：任务卡、实现结果、审查状态、必要风险和 Git 状态。
- Major：Product Brief、Technical Plan、回滚计划、实现与审查结果、Codex 验收、所有者决定。

不得复述整个代码库、复制大段日志或记录隐藏推理。检查结果只写命令、结论和最小必要错误摘要。

## 8. Verification and completion

完成必须同时满足：

- 改动与已批准范围一致；
- 所有规定检查已实际运行并记录结果；
- 未运行的检查明确标为 `NOT_RUN`，不得推测通过；
- Review Agent 已给出允许的状态；
- 文档、迁移和回滚要求已按任务等级处理；
- 分支、提交、工作树、推送和 PR 状态已准确记录；
- 需要的项目所有者批准已获得。

不得仅凭“代码已写完”宣称任务完成。

## 9. Failure and destructive actions

- 首次失败先保留证据并做范围内诊断。
- 同类失败重复出现、计划不再适用或风险扩大时停止实现并升级。
- 禁止在源码、日志、报告、提交或前端输入中放置凭据、令牌、个人联系信息或支付信息。
- 删除、迁移、覆盖、重置、强制推送、历史重写和生产变更前，必须解析准确目标、确认恢复路径，并取得所需授权。
- 外部工具或 Agent 不可用时生成可执行交接材料，并标记 `NOT_EXECUTED`，不得冒充已执行。

## 10. Upgrade loop

后续升级沿用同一流程：

1. 记录当前版本、真实运行状态、已知债务和升级目标。
2. 判断升级为 Quick、Standard 或 Major。
3. 对 Major 升级更新 Product Brief、Technical Plan、验收标准和回滚计划。
4. TRAE Coding Agent 实现，TRAE Review Agent 独立审查。
5. 命中触发条件时由 Codex 定向验收。
6. 项目所有者批准合并与部署。
7. 更新项目文档中的当前阶段、命令、风险和下一里程碑。

## 11. Permission matrix

| Action | GPT | Codex | TRAE Coding | TRAE Review | Project owner |
|---|---:|---:|---:|---:|---:|
| 提出产品方案 | Yes | Advise | Advise | Advise | Decide |
| 批准产品范围 | No | No | No | No | Yes |
| 制定重大技术计划 | No | Yes | No | Review | Approve |
| 编写任务代码 | No | By exception | Yes | No | Optional |
| 独立代码审查 | No | On trigger | No | Yes | Optional |
| 提交本地任务分支 | No | Yes | Yes | No | Yes |
| 推送任务分支 / 创建或更新 PR | No | Yes | If authorized | No | Yes |
| 合并 / 部署 | No | No | No | No | Yes |
| 强制推送 / 历史重写 / 远程删除 | No | Separate approval | Separate approval | No | Explicit approval |
