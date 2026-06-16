# New Project Quickstart

Use this checklist when you want to apply the SOP Dev Flow workflow to a fresh repository or retrofit it into an existing one.

## Recommended Path

If OpenCode bootstrap support is available, use it:

1. Open the target repository in OpenCode.
2. Run `/bootstrap-sop`.
3. Verify that `ops/`, `ops/templates/`, `ops/scripts/`, and optional `.github/` assets were scaffolded.
4. Review `ops/routing-rules.md` and `ops/operator-guide.md` for project-specific constraints.
5. Create a smoke-test task and confirm the workflow artifacts are generated correctly.

## Portable Manual Path

If bootstrap support is not available yet, copy these repo-local assets into the new project:

```text
ops/
  README.md
  sop-v1.1.md
  routing-rules.md
  operator-guide.md
  templates/
  scripts/
```

Then do the following:

1. Adjust `ops/routing-rules.md` for project-specific constraints.
2. Adjust `ops/operator-guide.md` for any project-specific run/build/test commands.
3. Create `ops/tasks/` if it does not exist.
4. Run `ops/scripts/start-task.sh TEST-001 bootstrap-smoke`.
5. Confirm the new task folder contains `packet.md`, `spec.md`, `worklog.md`, `review.md`, and `result.md`.
6. Delete the smoke-test task or convert it into the first real task.

## Existing Project Rollout

For an existing repository, do not try to convert every open thread of work at once. Add the workflow in layers:

1. Scaffold `ops/`, `ops/templates/`, and `ops/scripts/` into the existing repo.
2. Update `ops/routing-rules.md` with project-specific constraints, sensitive directories, and build/test expectations.
3. Update `ops/operator-guide.md` with the real commands maintainers use in that repo.
4. Create one bootstrap verification task with `ops/scripts/start-task.sh MIGRATE-001 sop-rollout-smoke`.
5. Use the workflow first on one low-risk real task.
6. After one successful pass, require packets for all non-trivial work.
7. After that, require `spec.md` for medium-risk, high-risk, or behavior-changing work.
8. Finally, enforce researcher, reviewer, security, QA, and ops closeout gates consistently.

## Existing Project Migration Checklist

- identify generated or forbidden paths that should never be staged
- document the repo's real test commands and smoke commands
- document any framework/API areas that should always trigger researcher-first handoff
- define when `spec.md` is mandatory versus optional
- start with one pilot task before applying the flow to all active work

## Existing Project First Task

Start each existing-project rollout with a migration verification task:

- Title: verify SOP rollout in existing repo
- Goal: prove the SOP assets fit the repo without blocking ongoing development
- Acceptance criteria:
  1. task scaffolding works in the existing repo
  2. routing rules reflect the repo's actual constraints
  3. build/test commands are recorded
  4. one low-risk task completes successfully through the new flow

## Minimum Repo Conventions

Every new project should have:

- planner-owned task packets in `ops/tasks/`
- `spec.md` for medium-risk, high-risk, or behavior-changing work
- SearXNG-backed research before non-trivial worker handoff
- reviewer, security, QA, and ops gates for non-trivial changes
- explicit commit authorization before git closeout

## First Task To Create

Start each new project with a bootstrap verification task:

- Title: verify SOP scaffold in new repo
- Goal: confirm scripts, templates, and routing docs are usable in the target project
- Acceptance criteria:
  1. task scaffolding works
  2. routing rules are adjusted for the target repo
  3. build/test commands are documented in the packet or operator guide

## Suggested Future Improvement

The cleanest long-term path is to make `/bootstrap-sop` the default entrypoint for new projects so that applying this workflow becomes:

1. create repo
2. open in OpenCode
3. run `/bootstrap-sop`
4. create first task

That keeps the process consistent and avoids copy/paste drift.
