# TRAE Review Agent Instruction

Review this task independently and read-only.

## Task and acceptance

{{TASK_AND_ACCEPTANCE}}

## Review boundary

- Base commit: `{{BASE_COMMIT}}`
- Head commit: `{{HEAD_COMMIT}}`
- Review: `git diff {{BASE_COMMIT}}...{{HEAD_COMMIT}}`

## Evidence

- Coding report: {{CODING_REPORT}}
- Checks already run: {{CHECK_RESULTS}}
- Relevant files: {{RELEVANT_FILES}}

## Review requirements

1. Inspect the actual diff and relevant surrounding code.
2. Check scope, correctness, regressions, error paths and maintainability.
3. Re-run only checks needed to verify the decision.
4. Do not modify files or create commits.
5. Return exactly:

```text
Status: APPROVED | CHANGES_REQUESTED | REJECTED
Blocking findings: <file:line and concise evidence, or None>
Checks: <commands and results>
Residual risk: <concise risk, or None>
```
