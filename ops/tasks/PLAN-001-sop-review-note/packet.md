# Task Packet

## Metadata
- Task ID: PLAN-001
- Title: Add sop-review note to implementation plan
- Priority: low
- Deadline: none
- Risk: low
- Owner model: openai/gpt-5.4
- Reviewer model: openai/gpt-5.4
- QA model: opencode/minimax-m2.5-free

## Goal (1 sentence)

Document that `sop-review` is the preferred automated review command in the plan repository.

## Scope
- In: `sop-implementation-plan.md` and `ops/tasks/PLAN-001-sop-review-note/`
- Out: scripts, tool code, and GitHub workflow changes

## Likely Files/Modules
- `sop-implementation-plan.md`
- `ops/tasks/PLAN-001-sop-review-note/`

## Acceptance Criteria (testable)
1. `sop-implementation-plan.md` mentions that `sop-review` is the preferred automated review command.
2. Task artifacts record implementation and verification evidence.
3. The change remains documentation-only and low risk.

## Test Plan
- Commands: `grep -n "sop-review" sop-implementation-plan.md`, `./ops/scripts/heartbeat-report.sh`
- Expected outcomes: the plan includes the note and heartbeat still reports task status correctly

## Dependencies

Repo-local SOP scaffold in `plans/`.

## Constraints

Keep the change low-risk and limited to documentation/task artifacts.

## Escalation Trigger

Missing note, inconsistent task artifacts, or ambiguity about the automated review command.

## Budget
- Time cap: 15 minutes
- Attempt cap (max 2): 1
- Token/cost cap (optional): n/a
