# New Project Quickstart

Use this checklist when you want to apply the SOP Dev Flow workflow to a fresh repository.

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
