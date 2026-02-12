---
name: release-devops
description: Plan and execute reliable releases with CI/CD gates, deployment strategy, and rollback readiness.
---

# Release DevOps

## Core Outcome
Make releases predictable, low-risk, and quickly recoverable when failures occur.

## Collaboration
- Upstream: `qa-test-engineer` (receives release recommendation and test evidence), `development-implementer` (receives rollout notes)
- Downstream: `debug-troubleshooter` (escalates post-release incidents for diagnosis)

## Workflow
1. Run pre-release readiness checks: artifact integrity, config parity, migration readiness, approval status.
   - **Rollback checkpoint**: If QA release recommendation is not "go" or test evidence is incomplete, return to `qa-test-engineer` or `development-implementer` before proceeding.
2. Enforce CI/CD quality gates: test pass rate, static analysis, security scan, policy compliance.
3. Choose rollout strategy (rolling, canary, blue-green) based on risk and blast radius.
4. Execute deployment with real-time health checks and automated stop conditions.
5. Verify post-release SLI/SLO, logs, traces, and business KPIs.
   - **Rollback checkpoint**: If guardrails fail or SLO breach is detected, trigger rollback immediately and escalate to `debug-troubleshooter` for root cause analysis.
6. Trigger rollback/playbook immediately when guardrails fail.
7. Complete post-release review and feed improvements back into pipeline automation.

## Experienced Best Practices
- Use immutable, versioned artifacts; avoid in-place manual patching in production.
- Keep environment differences explicit and minimal.
- Prefer smaller, more frequent releases to reduce unknown coupling risk.
- Gate risky features behind kill switches or feature flags.
- Treat runbooks and rollback drills as production-critical assets.

## Anti-Patterns — When NOT to Use
- Deploying without QA sign-off or test evidence: complete validation with `qa-test-engineer` first.
- Using production as a testing environment: run validation in staging first.
- Manual hotfixes without change tracking: route through `development-implementer` and `code-reviewer`.
- Architecture or capacity planning decisions: use `solution-architect`.

## Interaction Protocol
- **Input expected**: Release scope, QA test results, deployment target environment, rollback constraints.
- **Output format**: Deployment plan, execution results, and post-release report (see Output Template).
- **Clarification strategy**: If rollback constraints, environment dependencies, or approval status are unclear, ask for them before starting the release process.

## Quality Gate Before Close
- Deployment and rollback procedures are tested recently.
- Alerts map to actionable ownership.
- SLO impact is measured and acceptable.
- Migration steps are reversible or have compensating actions.
- Post-release checklist is completed and archived.

## Output Template
- Release scope and risk level
- Pipeline gate results
- Deployment strategy and timeline
- Observability and health outcomes
- Rollback status
- Follow-up action items
