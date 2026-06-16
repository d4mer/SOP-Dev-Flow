# Operator Guide

## Recommended Workflow

1. Start a fresh standalone server:
   - `~/.opencode/bin/opencode serve --hostname 127.0.0.1 --port <PORT>`
2. Use `/start-task` or `/start-hotfix` to create task artifacts.
3. Keep planning in the main assistant context; do not delegate packet creation, packet correction, spec approval, or status ownership to a planner subagent.
4. Fill the task packet before execution.
5. For medium-risk, high-risk, or behavior-changing work, fill `spec.md` and approve it before implementation.
6. For implementation, hotfix, framework/API, build/tooling, persistence, privacy, security, or user-reported regression work, run SearXNG-backed research before worker handoff.
7. Prefer `/run-task-loop <task-directory>` for planner-led delegated execution.
8. Prefer `/sop-review` over `/review` for automated review.
9. Let the `ops` subagent handle git staging and commits only after explicit user authorization.

## Task Directory Input

`run-task-loop` expects an exact task directory path, for example:

`ops/tasks/GAME-005-controls-readme-note`

## Delegation Model

- Planner: coordination only in the main assistant context
- Researcher: pre-worker external research via SearXNG
- Worker: implementation
- Reviewer: review and findings
- QA: verification
- Security: security review when needed
- Ops: heartbeat, workflow hygiene, and authorized git closeout

## Spec-Driven Development Rule

- Use packet-only mode for trivial documentation or low-risk housekeeping.
- Use packet plus `spec.md` for medium-risk, high-risk, or behavior-changing tasks.
- Treat `spec.md` as the approved behavior and design contract; update it when implementation changes the intended behavior.
- Keep tests aligned to the requirements listed in `spec.md`.

## Research Gate Rule

- Run a researcher gate before worker handoff for implementation, hotfix, framework/API, build/tooling, persistence, privacy, security, or user-reported regression work.
- Research output should provide concise sources, current guidance, risks, and concrete worker instructions.
- Skip the research gate only for purely local/docs/status tasks or trivial changes where external/current guidance adds no value.

## Operational Notes

- For low-risk documentation-only tasks, QA may be skipped if the planner explicitly records why.
- If `run-task-loop` appears to hang, check the task artifacts before assuming it failed. Delegated work may still have completed.
- If local model routing behaves oddly, restart the standalone server and retry once before changing config.
- Workers should not stage or commit as part of normal delivery; git closeout belongs to `ops` after explicit authorization.
