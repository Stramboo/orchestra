# AI 开发流程接入与使用

## 目录

1. 入口模式
2. 已有项目
3. 新项目
4. 日常角色路由
5. Codex 最小证据包
6. 项目升级

权限、任务分级和异常处理详见 [workflow.md](workflow.md)。

## 1. 入口模式

每次显式调用只选择一个主模式：

```text
BOOTSTRAP_EXISTING
BOOTSTRAP_NEW
CLASSIFY_TASK
CREATE_HANDOFF
PLAN_UPGRADE
PREPARE_CODEX_REVIEW
UPDATE_PROJECT_RULES
```

模式含义：

| 模式 | 产物 |
|---|---|
| `BOOTSTRAP_EXISTING` | 为已有仓库接入流程和项目规则 |
| `BOOTSTRAP_NEW` | 为新项目建立 Product Brief 到第一里程碑的路径 |
| `CLASSIFY_TASK` | 判断 Quick、Standard 或 Major |
| `CREATE_HANDOFF` | 生成当前角色所需的紧凑交接材料 |
| `PLAN_UPGRADE` | 规划事件驱动的下一阶段升级 |
| `PREPARE_CODEX_REVIEW` | 生成最小 Codex 审核证据包 |
| `UPDATE_PROJECT_RULES` | 基于真实仓库更新 `AGENTS.md` 建议 |

请求包含多个独立目标时，先拆分并确认执行顺序。

## 2. 已有项目

按以下顺序接入：

1. 解析真实项目根目录并检查 Git 状态；
2. 阅读现有 `AGENTS.md`、`README.md`、架构、决策和真实命令；
3. 保留所有未提交工作，不清理、不重置、不覆盖；
4. 只有用户明确要求时才复制 `AI_DEVELOPMENT_WORKFLOW.md`；
5. 目标已有同名文件时停止覆盖，报告差异；
6. 目标已有 `AGENTS.md` 时生成差异或合并建议，不静默替换；
7. 让 TRAE 整理技术栈、目录、命令、架构、改动和已知问题；
8. 根据真实证据替换 `AGENTS.md` 模板变量；
9. 只调用一次 Codex 审查项目基线、关键风险和下一里程碑；
10. 选择一个边界明确的 Standard 任务试运行；
11. 试运行通过后，再逐步纳入其他任务。

接入不要求重构现有架构，也不要求补齐所有可选文档。

## 3. 新项目

按以下顺序启动：

1. GPT 生成 Product Brief；
2. 项目所有者批准范围、非目标和产品验收标准；
3. 建立仓库并复制项目规则 assets；
4. 根据实际技术选择填写 `AGENTS.md`，不预填虚构命令；
5. Codex 一次性完成初始架构、验证方式和第一里程碑计划；
6. TRAE 编码 Agent 实现第一个可运行、可测试的纵向切片；
7. 独立 TRAE 上下文审查代码和回归；
8. 只有触发条件或里程碑关闭时才再次调用 Codex；
9. Codex push 任务分支并创建 PR；
10. 项目所有者批准合并和部署。

第一阶段不要搭建没有真实需求的完整平台、后端、账户、数据库或自动化系统。

## 4. 日常角色路由

```text
新 Idea 或产品方向             → GPT
已选方向的技术里程碑           → Codex
已有计划覆盖的具体实现         → TRAE 编码 Agent
代码与回归审查                 → TRAE 独立审查 Agent
架构风险、审查冲突或里程碑验收 → Codex
GitHub 任务分支 push 和 PR      → Codex
产品取舍、主分支合并和部署     → 项目所有者
```

Quick 任务默认不调用 Codex。Standard 任务优先复用现有计划。Major 任务先完成 Codex 计划和所有者批准。

## 5. Codex 最小证据包

调用 Codex 前提供：

```text
已批准的产品依据或任务来源
Technical Plan 引用
git diff --stat
修改文件列表
Review Status 和阻断问题
准确的检查命令和结果
需要 Codex 判断的具体问题
```

不要附带完整聊天、未变化模块、重复代码说明或无关日志。权限细节只引用 [workflow.md](workflow.md)，不在交接中重复权限矩阵。

Codex 发现具体风险后，可以说明原因并扩大检查范围。

## 6. 项目升级

以下事件触发升级：

- 项目所有者提出新方向；
- 里程碑完成；
- 用户反馈要求调整；
- 技术债阻塞开发、测试或维护；
- 安全、依赖、平台或部署条件需要变化。

使用：

```text
TRAE 整理当前版本、验证结果和已知问题
→ GPT 提供升级方向和产品取舍
→ 项目所有者选择值得推进的方向
→ Codex 只分析已选方向的技术影响、顺序、风险和回滚
→ 更新适用的 ROADMAP / DECISIONS / ARCHITECTURE
→ 按 Quick、Standard 或 Major 执行
→ 更新里程碑状态和后续候选项
```

不要让 Codex 深入规划项目所有者尚未选择的假设方向。多个相关升级尽量合并成一个里程碑计划。
