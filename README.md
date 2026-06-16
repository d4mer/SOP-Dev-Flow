# SOP Dev Flow

Repo-local SOP scaffold for planner-led agentic software delivery with researcher gates, task packets, review controls, QA handoff, and lightweight spec-driven development.

## What This Repo Is

This repository captures a practical operating system for AI-assisted software work in OpenCode.

It defines:
- a planner-led multi-agent delivery flow
- a pre-worker research gate for non-trivial implementation work
- repo-local task artifacts and templates
- review, QA, security, and rollback gates
- lightweight spec-driven development for non-trivial work
- bootstrapable SOP assets that can be reused in other repositories

## Workflow Summary

Core execution flow:

`Planner -> Researcher when needed -> Worker -> Reviewer -> Security if needed -> QA -> Ops -> Planner closeout`

For trivial work:
- use `packet.md`

For medium-risk, high-risk, or behavior-changing work:
- use `packet.md` plus `spec.md`
- approve the spec before implementation
- keep tests mapped to spec requirements

For implementation, hotfix, framework/API, build/tooling, persistence, privacy, security, or regression work:
- run a researcher gate before worker handoff
- incorporate research-backed constraints into the packet, spec, or worker prompt

## Repository Layout

```text
ops/
  sop-v1.1.md              Core SOP
  routing-rules.md         Detailed role, gate, and research routing guidance
  README.md                Ops-specific workflow reference
  operator-guide.md        Human/operator usage notes
  templates/               Task, spec, review, result, and incident templates
  scripts/                 Task creation and heartbeat helpers
  tasks/                   Per-task working directories
```

## Key Artifacts

- `ops/templates/task-packet.md`: execution packet with scope, acceptance criteria, test plan, budget, and spec gate
- `ops/templates/spec.md`: lightweight behavior and design contract for non-trivial work
- `ops/templates/review-form.md`: reviewer checklist including spec conformance and test traceability
- `ops/templates/result.md`: final task outcome and rollback note
- `ops/templates/incident.md`: hotfix incident tracking
- `ops/routing-rules.md`: planner-only, research, worker, review, QA, security, and ops gate policy

## Scripts

- `ops/scripts/start-task.sh <TASK-ID> <slug>`: create a new task folder with packet, spec, worklog, review, and result files
- `ops/scripts/start-hotfix.sh <slug>`: create a hotfix folder with incident, packet, spec, review, and result files
- `ops/scripts/heartbeat-report.sh`: report blocked tasks, ready-for-review tasks, and active hotfix incidents

## New Projects

Use `NEW_PROJECT_QUICKSTART.md` to apply this workflow to a fresh repository.

Preferred path:

1. Open the target repo in OpenCode.
2. Run `/bootstrap-sop`.
3. Review the scaffolded `ops/` assets.
4. Create a smoke-test task and verify the flow.

Fallback path:

- copy the `ops/` assets into the target repo
- adjust `routing-rules.md` and `operator-guide.md`
- run `ops/scripts/start-task.sh TEST-001 bootstrap-smoke`

## Recommended Usage

1. Start a fresh standalone OpenCode server.
2. Create a task with `start-task.sh` or a hotfix with `start-hotfix.sh`.
3. Fill `packet.md` in the main assistant context.
4. If the work is non-trivial, fill and approve `spec.md`.
5. For substantial implementation work, run the researcher gate before worker handoff.
6. Run the planner-led task loop.
7. Review, validate, and record results before merge.

## Design Principles

- planner coordinates, but does not directly implement
- planning stays in the main assistant context; it is not delegated to a planner subagent
- external research is routed through SearXNG before worker handoff when current guidance matters
- templates keep work inspectable and repeatable
- tests are the executable source of truth
- specs are lightweight and only required when justified by task risk or complexity
- security and rollback expectations are explicit, not implied

## Status

This repo is an active working SOP, not a polished framework release. Expect iteration as the workflow evolves.
