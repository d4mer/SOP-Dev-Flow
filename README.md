# SOP Dev Flow

Repo-local SOP scaffold for agentic software delivery with planner-led execution, task packets, review gates, QA handoff, and lightweight spec-driven development.

## What This Repo Is

This repository captures a practical operating system for AI-assisted software work in OpenCode.

It defines:
- a planner-led multi-agent delivery flow
- repo-local task artifacts and templates
- review, QA, security, and rollback gates
- lightweight spec-driven development for non-trivial work
- bootstrapable SOP assets that can be reused in other repositories

## Workflow Summary

Core execution flow:

`Planner -> Worker -> Reviewer -> Security if needed -> QA -> Ops`

For trivial work:
- use `packet.md`

For medium-risk, high-risk, or behavior-changing work:
- use `packet.md` plus `spec.md`
- approve the spec before implementation
- keep tests mapped to spec requirements

## Repository Layout

```text
ops/
  sop-v1.1.md              Core SOP
  routing-rules.md         Model routing and task type guidance
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

## Scripts

- `ops/scripts/start-task.sh <TASK-ID> <slug>`: create a new task folder with packet, spec, worklog, review, and result files
- `ops/scripts/start-hotfix.sh <slug>`: create a hotfix folder with incident, packet, spec, review, and result files
- `ops/scripts/heartbeat-report.sh`: report blocked tasks, ready-for-review tasks, and active hotfix incidents

## Recommended Usage

1. Start a fresh standalone OpenCode server.
2. Create a task with `start-task.sh` or a hotfix with `start-hotfix.sh`.
3. Fill `packet.md`.
4. If the work is non-trivial, fill and approve `spec.md`.
5. Run the planner-led task loop.
6. Review, validate, and record results before merge.

## Design Principles

- planner coordinates, but does not directly implement
- templates keep work inspectable and repeatable
- tests are the executable source of truth
- specs are lightweight and only required when justified by task risk or complexity
- security and rollback expectations are explicit, not implied

## Status

This repo is an active working SOP, not a polished framework release. Expect iteration as the workflow evolves.
