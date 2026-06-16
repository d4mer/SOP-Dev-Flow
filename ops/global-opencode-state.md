# Global OpenCode State

This file records the current global OpenCode workflow state that lives outside this git repository in `~/.config/opencode/`.

## Current Agent Models

- Planner: `openai/gpt-5.4`
- Planner fallback: `minimax-coding-plan/MiniMax-M2.7`
- Worker: `omlx/qwen3.6-35b`
- Reviewer: `openai/gpt-5.4`
- QA: `opencode/minimax-m2.5-free`
- Ops: `omlxmini/qwen3.5-9b`
- Security: `omlx/qwen3.6-35b`

## Current Command Roles

- `route` -> planner
- `bootstrap-sop` -> worker
- `start-task` -> worker
- `start-hotfix` -> worker
- `heartbeat` -> ops
- `security-scan` -> security
- `sop-review` -> reviewer
- `review` -> reviewer compatibility shim
- `run-task-loop` -> planner

## Planner Rule

- The planner coordinates only.
- The planner should not directly implement, review, or test.
- Execution is delegated to worker, reviewer, qa, ops, and security subagents.

## Known Runtime Notes

- Fresh standalone `opencode serve` processes are more reliable than reusing older sessions.
- `sop-review` is the preferred automated review command.
- `run-task-loop` successfully delegates work, but the CLI completion path can still hang on longer runs even when the underlying task succeeds.
