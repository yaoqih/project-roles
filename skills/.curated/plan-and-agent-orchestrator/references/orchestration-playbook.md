# Orchestration Playbook

## Table of Contents
1. Master Plan Template
2. Dispatch Ticket Template
3. Codex Execution Card Template
4. Evidence Ledger Template
5. Re-Planning Decision Rules
6. Parallel Dispatch Command Sheet
7. Documentation Alignment Checklist

## Master Plan Template
```markdown
# Program Goal
- Goal:
- Scope:
- Non-goals:

# Phase Tree
## Phase P1: <name>
- Outcome:
- Workstreams:
  - UI:
  - Core:
  - Infrastructure:
  - Test:
  - Docs:

## Branch Backlog
| Branch ID | Objective | Owner Skill | Dependencies | Done Criteria |
|---|---|---|---|---|
| P1-UI-01 |  |  |  |  |
| P1-CORE-01 |  |  |  |  |
```

## Dispatch Ticket Template
```markdown
## Dispatch Ticket: <task-id>
- Objective:
- Owner Skill:
- Inputs:
- Commands/Checks:
- Expected Artifacts:
- Risks:
- Exit Criteria:
```

## Codex Execution Card Template
```markdown
## Codex Execution Card: <task-id>
- Skill:
- Goal:
- Allowed Scope:
  - Files/paths:
  - Prohibited areas:
- Commands to run:
- Tests/checks to run:
- Evidence to capture:
- Completion criteria:
```

## Parallel Dispatch Command Sheet
```markdown
# Group tasks by no-overlap scope
## Group G1 (parallel)
- T1 path scope:
- T2 path scope:

## Commands
codex exec --cd <repo> "Use $<skill> to execute T1: <goal+criteria>" > .orchestrator/logs/T1.log 2>&1 &
PID_T1=$!
codex exec --cd <repo> "Use $<skill> to execute T2: <goal+criteria>" > .orchestrator/logs/T2.log 2>&1 &
PID_T2=$!

wait $PID_T1; RC_T1=$?
wait $PID_T2; RC_T2=$?
echo "T1=$RC_T1 T2=$RC_T2"

## Harvest
- Extract evidence snippets from each log.
- Mark pass/partial/fail by task.
- Re-plan from failed or partial tasks.
```

## Evidence Ledger Template
```markdown
| Task ID | Owner Skill | Evidence | Result | Next Action |
|---|---|---|---|---|
| P1-UI-01 | development-implementer | diff + unit tests | pass | move next |
| P1-QA-01 | qa-test-engineer | regression report | partial | split branch |
| P1-DOC-01 | technical-writer | updated docs paths | pass | close branch |
```

## Re-Planning Decision Rules
- If evidence is `pass`, close task and unlock dependent branch.
- If evidence is `partial`, split the task into smaller branches and redispatch.
- If evidence is `fail`, reopen root-cause analysis with `debug-troubleshooter`.
- If acceptance criteria are unclear, send back to `requirements-analyst`.
- If cross-module design conflict appears, escalate to `solution-architect`.
- If release risk rises, run `release-devops` before merging additional work.

## Documentation Alignment Checklist
- Governing docs constraints (for example `codex.md`, specs, ADRs, runbooks) are respected and traceable in output.
- Product and implementation docs reflect actual implemented behavior.
- Test scope and outcomes are recorded for each changed feature.
- Release and rollback notes updated when deployment behavior changes.
- Unverified items are explicitly listed with risk and owner.
