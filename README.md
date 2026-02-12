# Project Roles Skills

可复用的工程角色型技能集合，按 `skill-installer` 兼容结构组织：

- 技能根路径：`skills/.curated/<skill-name>/`
- 每个技能目录顶层必须包含：`SKILL.md`

## Repository Structure

```text
project-roles/
├── skills/
│   └── .curated/
│       ├── code-reviewer/
│       ├── debug-troubleshooter/
│       ├── development-implementer/
│       ├── qa-test-engineer/
│       ├── release-devops/
│       ├── requirements-analyst/
│       ├── solution-architect/
│       └── technical-writer/
└── README.md
```

## Install With Skill Installer

在 Codex 中可直接安装单个技能目录（GitHub tree URL）：

```text
$skill-installer install https://github.com/yaoqih/project-roles/tree/main/skills/.curated/solution-architect
```

批量安装时，重复给出多个技能路径即可（每个路径都指向一个含 `SKILL.md` 的目录）。

## Available Skills

1. `code-reviewer` - Review code changes for quality, security, performance, and maintainability with actionable, prioritized feedback.
2. `debug-troubleshooter` - Diagnose defects through evidence-driven investigation, root-cause analysis, and safe remediation.
3. `development-implementer` - Implement features end-to-end across application layers with production-ready quality.
4. `qa-test-engineer` - Design risk-based test strategies and validate release readiness across functional and non-functional dimensions.
5. `release-devops` - Plan and execute reliable releases with CI/CD gates, deployment strategy, and rollback readiness.
6. `requirements-analyst` - Convert ambiguous ideas into implementable, testable requirements with user stories and acceptance criteria.
7. `solution-architect` - Design evolvable system architecture balancing business goals, technical constraints, and delivery speed.
8. `technical-writer` - Produce clear, accurate, and maintainable technical documentation for APIs, systems, and user workflows.

## Manual Install (Fallback)

1. 复制 `skills/.curated/<skill-name>` 到本机 `~/.codex/skills/<skill-name>`
2. 重启 Codex 使新技能生效
