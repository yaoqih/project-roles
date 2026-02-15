---
name: plan-and-agent-orchestrator
description: Orchestrate multi-phase software delivery through tree-structured planning, specialist skill dispatch, Codex execution, and evidence-based re-planning.
---

# Plan And Agent Orchestrator

## Core Outcome
Drive delivery to a verifiable target state through a strict closed loop:
1. Build a tree-structured phase plan.
2. Dispatch specialist skills with clear done criteria.
3. Execute through Codex.
4. Evaluate evidence against acceptance criteria.
5. Re-plan until all gaps are closed.

## Collaboration
- **Upstream**: User goals, constraints, and success criteria; repository context (changed files, failing checks, pending defects); governing references (specs, ADRs, `AGENTS.md`, `codex.md`).
- **Downstream**: All specialist skills — routes work to the appropriate owner and collects evidence back.
- **Escalation**: If the orchestrator cannot resolve a cross-cutting conflict, surface it to the user with options and tradeoff analysis.

## Skill Routing Matrix
Route each executable task to exactly one primary owner skill:

| Skill | Route To When... |
|---|---|
| `requirements-analyst` | Scope is unclear, acceptance criteria are missing or untestable |
| `solution-architect` | Architecture decisions needed, cross-module tradeoffs, contract design |
| `development-implementer` | Implement, refactor, or configure code |
| `debug-troubleshooter` | Root cause of a defect is unknown |
| `code-reviewer` | Assess correctness, security, performance, maintainability |
| `qa-test-engineer` | Define and execute risk-based validation and regression |
| `release-devops` | Manage CI/CD gates, release strategy, rollback readiness |
| `technical-writer` | Update docs to match implemented behavior |

## Default Pipelines
Use these defaults unless the user requests a different sequence.

### A. Defect Closure
`debug-troubleshooter` → `development-implementer` → `qa-test-engineer` → `code-reviewer` → `technical-writer` (if behavior or runbook changed)

### B. Feature Delivery
`requirements-analyst` → `solution-architect` (when needed) → `development-implementer` → `code-reviewer` → `qa-test-engineer` → `release-devops` → `technical-writer`

### C. Hardening And Release
`code-reviewer` → `qa-test-engineer` → `release-devops` → `technical-writer`

## Workflow
1. **Collect inputs**
   - User goal, constraints, and success criteria.
   - Repository status: changed files, failing checks, pending defects, release constraints.
   - Governing references: specs, ADRs, `AGENTS.md`, `codex.md` (if present).
   - Existing test, CI, and release signals.
   - Record assumptions explicitly if any input is missing; continue with the lowest-risk plan.

2. **Build tree-structured plan**
   Model work as a tree, not a flat list:
   - Level 0: Program goal.
   - Level 1: Delivery phases.
   - Level 2: Workstreams (feature area, service, platform, quality, docs, release).
   - Level 3: Executable task tickets, each defined with:
     - `objective`, `owner_skill`, `dependencies`, `commands_or_checks`, `expected_artifacts`, `done_criteria`
   - Use `references/orchestration-playbook.md` templates for consistent formatting.

3. **Select pipeline and assign skills**
   Pick the matching default pipeline (or a user-defined sequence). Assign one primary owner skill per task.

4. **Prepare Codex execution cards**
   For each task, produce a card containing: skill, goal, allowed file scope, commands to run, tests/checks, evidence to capture, and completion criteria. See Codex Execution Reference below.

5. **Execute via Codex**
   Run tasks using Codex commands. For independent tasks, use parallel dispatch when file scopes do not overlap.

6. **Evaluate evidence at phase gates**
   Compare outputs against acceptance criteria and the alignment checklist.
   - **Pass**: close task, unlock dependent branches, advance to next phase.
   - **Partial**: split into smaller branches and redispatch.
   - **Fail**: rollback to checkpoint, reopen root-cause analysis with `debug-troubleshooter`.

7. **Re-plan from evidence**
   Update the plan tree based on observed results. Do not advance phase gates without evidence. If acceptance criteria become unclear, send back to `requirements-analyst`. If cross-module design conflicts emerge, escalate to `solution-architect`.

8. **Handle mid-flight scope changes**
   - Freeze in-progress tasks at their current checkpoint.
   - Re-run steps 1–3 with updated scope.
   - Diff the new plan against the old; carry forward completed evidence and discard only invalidated branches.

## Codex Execution Reference

### Invocation Contract
- Treat `codex.md` as command-format reference, not business requirement source.
- Extract and use the command style defined in `codex.md` when building execution cards.
- Keep requirement/architecture truth from project specs, ADRs, tickets, and user directives.
- Always emit executable Codex commands per task before dispatch.

### Command Templates
```bash
# Interactive single task
codex --cd <repo-path> "Use $<skill-name> to complete <task-id>: <goal and done criteria>"

# Non-interactive single task
codex exec --cd <repo-path> "Use $<skill-name> to complete <task-id>: <goal and done criteria>"

# Continue prior context
codex exec resume --last "Use $<skill-name> to continue <task-id> and finish remaining criteria"
```
If multiple directories are required, append `--add-dir <path>` entries.

### Parallel Multi-Agent Dispatch
Support parallel execution when tasks are independent.

**Preconditions**:
- Split tasks by non-overlapping file/path ownership.
- Freeze shared contracts first (API/schema/interface docs).
- Define merge order and conflict owner before running.

**Mode A — Multi-terminal**: Open one terminal per task. Run one Codex command per terminal with unique task ID and log file.

**Mode B — Background parallel**:
```bash
mkdir -p .orchestrator/logs

codex exec --cd <repo-path> "Use $development-implementer to deliver T1 ..." \
  > .orchestrator/logs/T1.log 2>&1 &
PID_T1=$!

codex exec --cd <repo-path> "Use $qa-test-engineer to validate T2 ..." \
  > .orchestrator/logs/T2.log 2>&1 &
PID_T2=$!

wait $PID_T1; RC_T1=$?
wait $PID_T2; RC_T2=$?
echo "T1=$RC_T1 T2=$RC_T2"
```
Collect logs, map each result to its branch ticket, then re-plan.

## Experienced Best Practices
- Plan first, then execute with Codex; do not stop at planning only.
- Define required evidence before execution, not after.
- Re-dispatch only from observed evidence, not assumptions.
- Keep code and documentation updates in the same closed loop.
- Treat project governing docs (`codex.md`, specs, ADRs, runbooks) as execution contracts.
- Update only task-related existing docs unless user explicitly asks for new sections.
- If implementation must diverge from docs, record the reason and update docs explicitly.
- When scope changes mid-flight, diff the new plan against the old rather than replanning from scratch.

## Anti-Patterns — When NOT to Use
- **Single-skill task with clear scope**: dispatch directly to the specialist skill without orchestration overhead.
- **Pure exploration or research**: investigate first, then return here when there is a deliverable to plan.
- **Trivial fix or one-file change**: use `debug-troubleshooter` or `development-implementer` directly.

### Execution Anti-Patterns
Avoid these during orchestration:
- Implementing without acceptance criteria.
- Skipping test evidence or hiding unverified items.
- Returning only a plan without Codex execution.
- Parallelizing tasks that edit the same files without ownership split.
- Updating code without updating affected docs.
- Assigning one task to multiple primary owners.
- Running a single large batch without phase gates.

## Interaction Protocol
- **Input expected**: User goal with success criteria, repository path, and any governing references (specs, `codex.md`, ADRs).
- **Output format**: Phase plan, dispatch list, evidence summary, and re-plan decisions (see Output Template).
- **Clarification strategy**: If the goal is ambiguous, success criteria are missing, or governing references conflict, ask explicitly before building the plan. Record unresolved items as assumptions with risk labels.

## Quality Gate Before Phase Advancement
Each phase must satisfy all applicable gates before advancement:

| Gate | Criteria |
|---|---|
| Functional | Feature behavior matches acceptance criteria |
| Quality | Relevant tests executed, regressions assessed |
| Alignment | Docs reflect actual behavior and decisions |
| Release | CI/CD and rollback plan validated (when deployment is in scope) |

### Feature Closure Checklist
Mark a feature branch as closed only when all evidence exists:
- **Requirement evidence**: story or acceptance criteria reference.
- **Implementation evidence**: code diff and key design decisions.
- **Validation evidence**: tests and regression results.
- **Review evidence**: `code-reviewer` findings resolved or consciously accepted.
- **Documentation evidence**: updated task-related docs and behavior notes.

## Output Template
- Current phase plan and next-phase preview
- Dispatch list (`task → owner skill → expected evidence → codex command`)
- Parallelization map (`task groups`, `no-overlap scopes`, `merge order`)
- Evidence summary (`pass`, `partial`, `fail` per task)
- Re-plan decisions and rationale
- Remaining risks and unresolved assumptions
