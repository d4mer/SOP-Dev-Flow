# Ops Workflow Assets

This directory contains repo-local planning, task, review, and validation assets for the SOP Dev Flow workflow.

## Directory Layout

- `sop-v1.1.md` - concise SOP summary
- `routing-rules.md` - detailed role and gate routing for planner, researcher, worker, reviewer, security, QA, and ops flow
- `operator-guide.md` - human/operator workflow notes
- `templates/` - canonical markdown templates used by scripts and task tooling
- `scripts/` - shell helpers for creating tasks and listing task status
- `tasks/` - task packets, worklogs, reviews, and results

## When To Use The Scripts

Prefer the built-in OpenCode tools when available:

- `create-task` for task scaffolding
- `heartbeat` for task status summaries
- `validate-packet` for packet completeness checks

Use `ops/scripts/` as portable repo-local fallbacks when those tools are not available:

- `ops/scripts/start-task.sh TASK-ID short-slug`
- `ops/scripts/start-hotfix.sh short-slug`
- `ops/scripts/heartbeat-report.sh`

## Task Packet Lifecycle

1. Main assistant/planner creates or corrects `packet.md` and `spec.md` in the current context. Do not launch a planner subagent for planning, task selection, packet creation, packet correction, spec approval, or status-doc ownership.
2. For implementation, hotfix, framework/API, build/tooling, persistence, privacy, security, or user-reported regression work, researcher performs SearXNG-only pre-worker research and returns sources plus precise implementation guidance.
3. Main assistant/planner incorporates the research guidance into the packet, spec, or worker prompt.
4. Worker performs a concise reasoning checkpoint before editing, then implements from the packet and records evidence in `worklog.md` and `result.md`.
5. Reviewer records findings in `review.md` and checks alignment with the packet, approved spec, and research guidance.
6. Security reviews medium/high risk or sensitive changes.
7. QA validates after reviewer/security pass.
8. Ops subagent checks task hygiene, stale/blocker status, packet completeness, and current-state consistency.
9. Main assistant/planner updates planner-owned status docs only.
10. After explicit user authorization, ops subagent owns git staging and commits. Ops must inspect status, diff, recent log, staged diff, and forbidden/generated paths, then stage only allowlisted files.

The main assistant/planner must remain planner-only before and after context compaction. It must not implement code, run implementation validation, stage files, commit, or substitute for worker/reviewer/security/QA/ops agents; it plans, updates planner-owned docs, routes work, and summarizes gate results.

## Worker Tooling Failures

Worker prompts should require a short reasoning checkpoint before edits: root-cause hypothesis, state/data-flow path, smallest fix, alternatives rejected, and tests mapped to risks. Workers should report concise conclusions only, not hidden chain-of-thought.

Workers should fail fast on editing-tool issues. If a worker cannot write a file because of escaping or invalid tool payloads, it must stop after two attempts and report a structured blocker instead of switching to heredocs, Python scripts, or shell redirection. The planner can then simplify the patch, split the task, or authorize a different implementation path.

## External Research Safety

Use SearXNG for all external searches and web fetches. Do not use native web fetch/search tools for external content because prompt-injection or hostile-page content can contaminate agent context.

Workers and researchers should use SearXNG whenever a task needs fresh ecosystem information, framework/API details, build-tool behavior, examples, or difficult debugging context. If SearXNG is unavailable, stop and report a blocker rather than falling back to native web fetch/search tools.

For implementation, hotfix, framework/API, build/tooling, persistence, privacy, security, or user-reported regression tasks, run the SearXNG research gate before worker handoff by default. Skip only for purely local/docs/status tasks or trivial changes where external/current guidance would not improve the instructions, and record the skip reason.

## Template Files

The files in `ops/templates/` are intentionally generic. They should stay stable so scripts can copy them into new task folders.

If a task requires richer sections, update the copied task file, not the template, unless the new section should apply to all future tasks.
