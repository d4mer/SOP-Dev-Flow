# SOP Implementation Plan

## 1. Objective

Implement an OpenCode-based Agentic Coding Team SOP with:

1. a global control plane in OpenCode
2. a repo-local SOP scaffold for each project
3. a bootstrap workflow that applies repo-local SOP assets to both existing and new repositories
4. a skill-assisted setup flow so the bootstrap behavior is reusable and consistent

This plan is intentionally structured to avoid mixing global OpenCode config with repository-owned workflow files.

## 2. Design Principles

1. Global vs local separation
   - Global OpenCode assets live in `~/.config/opencode/`
   - Repo-owned workflow assets live inside each repository
2. Use documented OpenCode locations
   - Agents: `~/.config/opencode/agents/`
   - Commands: `~/.config/opencode/commands/`
   - Tools: `~/.config/opencode/tools/`
   - Skills: `~/.config/opencode/skills/<name>/SKILL.md`
3. Use markdown agent definitions
   - Agent definitions will be `.md` files, not JSON agent config
4. Use real deterministic tools
   - Custom tools should actually inspect, scaffold, and verify
   - Avoid placeholder tools that only describe what should happen
5. Verification after every layer
   - Every component should have an explicit smoke test
6. Idempotent repo bootstrap
   - Existing files are never silently overwritten
   - Output should clearly distinguish created, skipped, and conflicted files

## 3. Final Architecture

### 3.1 Global Control Plane

Stored in `~/.config/opencode/`:

- `agents/`
- `commands/`
- `tools/`
- `skills/`

This layer provides shared orchestration logic usable in any repository.

### 3.2 Repo-Local SOP Assets

Scaffolded into each target repo:

- `ops/`
- `ops/templates/`
- `ops/scripts/`
- `ops/tasks/`
- `.github/` optionally
- optional repo `AGENTS.md`

This layer provides the actual project workflow and artifacts.

## 4. Global Assets to Create

### 4.1 Global Agents

Create in `~/.config/opencode/agents/`:

1. `planner.md`
2. `worker.md`
3. `reviewer.md`
4. `qa.md`
5. `ops.md`
6. `security.md`

### 4.2 Global Commands

Create in `~/.config/opencode/commands/`:

1. `start-task.md`
2. `start-hotfix.md`
3. `heartbeat.md`
4. `route.md`
5. `security-scan.md`
6. `review.md`
7. `bootstrap-sop.md`

### 4.3 Global Tools

Create in `~/.config/opencode/tools/`:

1. `route-task.ts`
2. `validate-packet.ts`
3. `create-task.ts`
4. `heartbeat.ts`
5. `bootstrap-sop.ts`

### 4.4 Global Skill

Create in `~/.config/opencode/skills/sop-bootstrap/SKILL.md`.

This skill defines the policy and workflow for repo-local SOP scaffolding.

## 5. Repo-Local Assets to Scaffold

### 5.1 SOP Documents

Create in repo `ops/`:

1. `ops/sop-v1.1.md`
2. `ops/routing-rules.md`

### 5.2 Templates

Create in `ops/templates/`:

1. `task-packet.md`
2. `status-update.md`
3. `review-form.md`
4. `result.md`
5. `incident.md`

### 5.3 Scripts

Create in `ops/scripts/`:

1. `start-task.sh`
2. `start-hotfix.sh`
3. `heartbeat-report.sh`

### 5.4 GitHub Assets

Optionally create:

1. `.github/pull_request_template.md`
2. `.github/workflows/sop-gates.yml`

### 5.5 Task Layout Convention

Standardize all task artifacts to:

- `ops/tasks/<TASK-ID>-<slug>/packet.md`
- `ops/tasks/<TASK-ID>-<slug>/worklog.md`
- `ops/tasks/<TASK-ID>-<slug>/review.md`
- `ops/tasks/<TASK-ID>-<slug>/result.md`

## 6. Skill-Assisted Bootstrap Design

### 6.1 Role of the Skill

The `sop-bootstrap` skill should define:

1. what belongs globally vs locally
2. what repo-local files must exist
3. naming conventions
4. idempotency rules
5. verification rules
6. optional `.github/` inclusion behavior

### 6.2 Role of the Command

The `/bootstrap-sop` command should:

1. load the `sop-bootstrap` skill
2. inspect the current repository
3. scaffold missing SOP assets into the repo
4. avoid silent overwrites
5. run verification
6. summarize results

### 6.3 Role of the Tool

`bootstrap-sop.ts` should be the deterministic implementation layer that:

1. identifies the current repo root
2. creates missing folders/files
3. tracks created, skipped, and conflict states
4. validates the scaffold
5. returns structured results

### 6.4 Why use all three

- Skill = policy and reusable instructions
- Command = user-facing entrypoint
- Tool = deterministic execution

This is the cleanest fit for OpenCode.

## 7. Model Strategy

The implementation should use the intended model assignments below.

### Agent model mapping

1. Planner
   - `openai/gpt-5.4`
2. Worker
   - Primary: `omlx/qwen3-coder`
   - Fallback and escalation: `minimax-coding-plan/MiniMax-M2.7`
3. Reviewer
   - `omlx/qwen3.5-27b`
4. QA
   - `opencode/minimax-m2.5-free`
5. Ops
   - `omlxmini/qwen3.5-9b`
6. Security
   - `omlx/qwen3.5-27b`

### Routing summary

- Architecture, planning, and escalation-sensitive work: `openai/gpt-5.4`
- General implementation: `omlx/qwen3-coder`
- Worker fallback or broader reasoning on implementation stalls: `minimax-coding-plan/MiniMax-M2.7`
- Review and security: `omlx/qwen3.5-27b`
- QA validation: `opencode/minimax-m2.5-free`
- Ops, heartbeat, and summaries: `omlxmini/qwen3.5-9b`

### Prerequisite verification

Before implementation, confirm these exact model IDs are available in OpenCode config.

## 8. Agent Design Rules

All custom agents should:

1. use `.md` frontmatter definitions
2. prefer `permission` over deprecated `tools`
3. have narrow, well-scoped roles
4. use task permissions to limit which subagents they may invoke
5. avoid mixing implementation and review duties

### Intended responsibilities

1. Planner
   - intake
   - packet creation rules
   - routing
   - gate enforcement
   - escalation
   - coordination only; does not directly implement, review, or test
2. Worker
   - implementation only
   - updates task result and worklog
   - does not merge
3. Reviewer
   - acceptance review
   - regression and risk review
   - invokes security when required
4. QA
   - test verification
   - regression validation
   - reports gaps
5. Ops
   - heartbeat and status summaries
   - stale and blocked tracking
6. Security
   - read-only security analysis
   - dependency and config review
   - pass and fail findings

## 9. Command Design Rules

Commands should orchestrate, not embed too much duplicate policy.

Planner rule:
- the planner coordinates and delegates
- execution commands should run on the appropriate subagent rather than having the planner perform the action itself

### Main commands

1. `/route`
    - classify task
    - select model and escalation notes
2. `/start-task`
    - create task folder from templates inside current repo via an execution subagent
3. `/start-hotfix`
    - create hotfix task structure via an execution subagent
    - minimal-change lane
4. `/heartbeat`
   - summarize task status from repo task artifacts
5. `/security-scan`
     - run or coordinate security review flow
 6. `/sop-review`
      - preferred automated review command via reviewer
 7. `/review`
      - compatibility shim for the old command name
 8. `/bootstrap-sop`
      - scaffold repo-local SOP assets into the current repository via an execution subagent
 9. `/run-task-loop`
      - planner-led workflow command that delegates implementation, review, and QA in sequence

### Most important command

`/bootstrap-sop` is the bridge between global config and repo-local rollout.

## 10. Tool Design Rules

### 10.1 `route-task.ts`

Should:
- accept task description and optional risk
- select a real configured model
- return route reason and escalation triggers

### 10.2 `validate-packet.ts`

Should:
- read a packet file
- confirm required sections exist and are populated
- return missing and incomplete fields

### 10.3 `create-task.ts`

Should:
- create `ops/tasks/<TASK-ID>-<slug>/`
- copy or generate canonical files
- support normal task and hotfix modes

### 10.4 `heartbeat.ts`

Should:
- inspect `ops/tasks/`
- report blocked, ready, stale, and inconsistent tasks

### 10.5 `bootstrap-sop.ts`

Should:
- identify repo root
- create repo-local SOP directories and files
- optionally include `.github/`
- not overwrite silently
- verify results
- return structured output

## 11. Repo Scaffold Rules

### 11.1 Idempotency

Bootstrap must be safe to re-run.

Rules:
1. create missing files
2. skip existing files by default
3. report conflicts explicitly
4. only overwrite with explicit opt-in

### 11.2 GitHub Assets

`.github/` should be optional because not every repo may want workflow enforcement immediately.

Recommended behavior:
- ask if GitHub assets should be included when absent
- support a flag or argument later if needed

### 11.3 Ownership Boundaries

1. Global OpenCode config owns:
   - agents
   - commands
   - tools
   - skills
2. Repo owns:
   - `ops/`
   - `.github/`
   - task artifacts
   - repo `AGENTS.md`

## 12. SOP Document Improvements

Before implementation, the original plan should be normalized into the new architecture.

### Required changes

1. replace mixed global and local path assumptions
2. replace undocumented `.agents/` global path usage
3. rewrite model routing to match configured models
4. standardize task directory naming
5. remove placeholder tool behavior
6. move repo scaffolding into a bootstrap workflow
7. clarify ownership of each generated artifact
8. strengthen verification criteria

## 13. GitHub Workflow Improvements

The existing workflow idea is sound, but enforcement should check values, not just section headers.

### Recommended validations

1. Task ID present
2. exactly one risk level selected or equivalent explicit value present
3. reviewer outcome present
4. rollback trigger, method, and owner populated when required
5. security result populated for medium and high risk
6. two approvals required for high-risk PRs
7. acceptance criteria completed for ready-to-merge PRs

## 14. Verification Plan

### 14.1 Global Agents

Verify:
1. files exist in `~/.config/opencode/agents/`
2. frontmatter parses
3. models are valid
4. permissions behave as intended
5. agents appear in OpenCode

### 14.2 Global Commands

Verify:
1. files exist in `~/.config/opencode/commands/`
2. commands appear in `/`
3. command prompt templates resolve
4. subagent behavior works where expected

### 14.3 Global Tools

Verify:
1. files exist in `~/.config/opencode/tools/`
2. OpenCode loads them
3. schema validation works
4. success and failure cases produce useful output

### 14.4 Skill

Verify:
1. `~/.config/opencode/skills/sop-bootstrap/SKILL.md` exists
2. name matches folder name
3. description is specific enough for discovery
4. skill appears in available skills
5. command or agent can load it

### 14.5 Repo Bootstrap

Verify:
1. expected directories exist
2. expected files exist
3. scripts are executable
4. task folder naming is correct
5. no existing files were overwritten silently

### 14.6 GitHub Assets

Verify:
1. PR template renders correctly
2. workflow YAML is valid
3. workflow logic fails on missing required fields
4. high-risk approval rules behave correctly

## 15. Rollout Strategy

### Existing repositories

For each existing repo:

1. open the repo
2. run `/bootstrap-sop`
3. review created, skipped, and conflict output
4. verify scaffold
5. optionally enable `.github/` assets

### New repositories

Recommended standard flow:

1. create repo
2. run `/init`
3. run `/bootstrap-sop`

Optional alternative:
- create repos from a template repo that already contains `ops/` and `.github/`

## 16. Recommended Implementation Order

### Phase 0

Validate:
1. runtime expectations
2. model mapping
3. plugin loading assumptions
4. GitHub asset inclusion policy

### Phase 1

Create global skill:
1. `sop-bootstrap`

### Phase 2

Create global agents:
1. planner
2. worker
3. reviewer
4. qa
5. ops
6. security

### Phase 3

Create global tools:
1. `route-task.ts`
2. `validate-packet.ts`
3. `create-task.ts`
4. `heartbeat.ts`
5. `bootstrap-sop.ts`

### Phase 4

Create global commands:
1. route
2. start-task
3. start-hotfix
4. heartbeat
5. security-scan
6. review
7. bootstrap-sop

### Phase 5

Prepare repo-local scaffold content:
1. SOP docs
2. templates
3. scripts
4. optional GitHub assets

### Phase 6

Bootstrap one test repository:
1. apply scaffold
2. verify output
3. refine if needed

### Phase 7

Roll out to existing repositories.

## 17. Deliverables

### Global deliverables

- 6 markdown agents
- 7 markdown commands
- 5 TypeScript tools
- 1 skill

### Repo-local deliverables

- `ops/` docs
- `ops/templates/`
- `ops/scripts/`
- `ops/tasks/` convention
- optional `.github/` assets

### Process deliverables

- deterministic bootstrap path
- reusable setup for new repos
- repeatable migration path for existing repos
- verification checklist per component

## 18. Final Recommendation

Implement this as:

1. global OpenCode skill
2. global bootstrap command
3. global bootstrap tool
4. repo-local scaffold payload

This gives you:
- reuse
- discoverability
- deterministic execution
- compatibility with both existing and future repositories

## 19. Next Step

The next step after this planning phase is to convert this into an execution checklist with exact files, exact responsibilities, and exact verification steps for each file before implementation begins.
