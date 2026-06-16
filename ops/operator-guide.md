# Operator Guide

## Recommended Workflow

1. Start a fresh standalone server:
   - `~/.opencode/bin/opencode serve --hostname 127.0.0.1 --port <PORT>`
2. Use `/start-task` or `/start-hotfix` to create task artifacts.
3. Fill the task packet before execution.
4. For medium-risk, high-risk, or behavior-changing work, fill `spec.md` and approve it before implementation.
5. Prefer `/run-task-loop <task-directory>` for planner-led delegated execution.
6. Prefer `/sop-review` over `/review` for automated review.

## Task Directory Input

`run-task-loop` expects an exact task directory path, for example:

`ops/tasks/GAME-005-controls-readme-note`

## Delegation Model

- Planner: coordination only
- Worker: implementation
- Reviewer: review and findings
- QA: verification
- Security: security review when needed
- Ops: heartbeat and status

## Spec-Driven Development Rule

- Use packet-only mode for trivial documentation or low-risk housekeeping.
- Use packet plus `spec.md` for medium-risk, high-risk, or behavior-changing tasks.
- Treat `spec.md` as the approved behavior and design contract; update it when implementation changes the intended behavior.
- Keep tests aligned to the requirements listed in `spec.md`.

## Operational Notes

- For low-risk documentation-only tasks, QA may be skipped if the planner explicitly records why.
- If `run-task-loop` appears to hang, check the task artifacts before assuming it failed. Delegated work may still have completed.
- If local model routing behaves oddly, restart the standalone server and retry once before changing config.
