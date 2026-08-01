# TRAE Coding Agent Instruction

## Task

{{TASK_GOAL}}

## Scope

- Allowed: {{ALLOWED_SCOPE}}
- Do not change: {{NON_GOALS}}

## Acceptance

{{ACCEPTANCE_CRITERIA}}

## Repository context

- Relevant files: {{RELEVANT_FILES}}
- Follow existing `AGENTS.md` and repository conventions.
- Preserve unrelated user changes.

## Checks

Run only verified project commands:

{{CHECK_COMMANDS}}

## Git handoff

When the task is complete:

1. Commit all task changes in one or a small number of focused commits.
2. Leave the worktree clean, or clearly identify isolated unrelated changes.
3. Report:
   - base commit;
   - head commit;
   - files changed;
   - checks and exact results;
   - remaining risk or `None`.
4. Do not push, merge, deploy, or expand scope unless explicitly authorized.
