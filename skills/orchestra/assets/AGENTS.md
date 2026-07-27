# {{PROJECT_NAME}} Agent Rules

## 1. Project identity

- Project goal: {{PROJECT_GOAL}}
- Current phase: {{CURRENT_PHASE}}
- Primary language and stack: {{PRIMARY_LANGUAGE_AND_STACK}}

只实现已批准目标。非目标、未来设想和邻近优化不自动进入当前任务范围。

## 2. Reading order and sources

开始工作前按顺序读取：

1. 项目所有者当前指令。
2. `AI_DEVELOPMENT_WORKFLOW.md`。
3. 当前任务已批准的 Product Brief、Technical Plan 或任务卡。
4. 本文件。
5. 与任务直接相关的代码、测试、配置和文档。

冲突时停止扩大范围，记录证据并请求项目所有者决定。

## 3. Repository map and protected paths

- Sensitive or protected paths: {{SENSITIVE_PATHS}}
- Deployment rules: {{DEPLOYMENT_RULES}}

首次采用本模板时，必须从仓库证据填写项目结构、真实入口和受保护路径。不得猜测目录职责。未经明确授权，不修改敏感路径、生产配置、凭据存储、用户数据或部署目标。

## 4. Real commands

| Purpose | Verified command |
|---|---|
| Run | `{{RUN_COMMAND}}` |
| Test | `{{TEST_COMMANDS}}` |
| Lint | `{{LINT_COMMANDS}}` |
| Typecheck | `{{TYPECHECK_COMMANDS}}` |
| Build | `{{BUILD_COMMANDS}}` |

采用本模板时，从仓库脚本、CI 或维护文档验证并替换全部变量。删除不适用的命令行，不得发明命令。未运行的检查标记为 `NOT_RUN`。

## 5. Task classification overrides

默认使用 `AI_DEVELOPMENT_WORKFLOW.md` 的 Quick、Standard、Major 分级。以下项目事实优先把任务升级：

- 修改公共接口、持久化数据、认证授权、安全边界或部署流程；
- 触及本文件列出的敏感路径；
- 跨越多个核心模块或无法用现有检查覆盖；
- 回滚困难、会中断服务或影响真实用户数据；
- 现有计划与仓库事实不一致。

若项目另有已验证的分级例外，在此处补充；没有证据时不得降级。

## 6. Codex triggers and evidence

只有重大任务、里程碑、计划偏离、审查争议、关键失败或项目所有者明确要求时调用 Codex。提交最小证据包：

- 已批准计划或任务卡；
- 相关文件与精简 diff；
- 实际运行的检查及结果；
- 最小失败日志；
- 未决问题、风险和所需决定；
- 当前分支、提交、工作树、推送与 PR 状态。

不得要求 Codex 为普通小改动重新浏览整个仓库。

## 7. TRAE Coding Agent

- 只修改任务允许的文件和行为。
- 先确认工作树与已有用户改动，再开始写入。
- 匹配现有代码风格，不顺手重构无关代码。
- 运行本文件列出的相关真实命令。
- 使用对应任务模板提交精简实现报告。
- 发现范围扩大、计划冲突或破坏性风险时停止并升级。

## 8. TRAE Review Agent

- 必须与本任务 Coding Agent 独立。
- 根据验收标准、相关 diff、代码和实际检查结果审查。
- 审查状态只能是 `APPROVED`、`CHANGES_REQUESTED` 或 `REJECTED`。
- Blocking Findings 和 Residual Risk 没有内容时写 `None`。
- 不以重写实现代替独立审查；需要修改时退回 Coding Agent。

## 9. Git, GitHub, merge, and deployment

- 每个任务使用独立分支，并保持提交范围单一。
- 同一 worktree 只允许一个活跃写入者；顺序 Agent 交接前记录 Git 状态。
- 并行写入使用不同 worktree。
- 保留并隔离与任务无关的未提交改动。
- Codex 可提交、推送任务分支并创建或更新 PR。
- 项目所有者批准合并和部署。
- 强制推送、历史重写、远程删除和生产变更需要单独明确授权。
- Deployment rules: {{DEPLOYMENT_RULES}}

## 10. Sensitive data and destructive actions

- 不在源码、日志、报告、提交、PR 正文或前端输入中放置令牌、密码、个人联系信息或支付信息。
- 使用项目认可的环境变量或秘密管理机制。
- 删除、覆盖、迁移、重置或生产操作前，确认准确目标、备份或恢复路径及所需授权。
- 外部 Agent 或服务不可用时只生成交接材料，并标记 `NOT_EXECUTED`。

## 11. Compact handoff

- Quick：只使用固定五行报告。
- Standard：只报告目标、改动、检查、独立审查、风险和 Git 状态。
- Major：附已批准计划、回滚方案和必要的 Codex 验收结论。
- 不复制大段日志，不复述已知背景，不记录隐藏推理。

## 12. Completion

仅当以下条件均满足时宣称完成：

- 实现符合批准范围和验收标准；
- 所需真实命令已运行，结果被准确记录；
- 独立审查已完成；
- 风险、偏差和 `NOT_RUN` 项已显式说明；
- Git、GitHub、合并和部署状态没有被夸大；
- 所需项目所有者授权已获得。
