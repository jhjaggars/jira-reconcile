# Jira Reconcile

Reusable Agent Skills for shaping Jira roadmaps and reconciling roadmap state
against repository, GitHub, deployment, and qualification evidence.

## Skills

- [`roadmap-outcome-builder`](skills/roadmap-outcome-builder/SKILL.md) - shapes
  stakeholder ideas into customer-facing Jira roadmap outcomes and hands locked
  outcomes to reconciliation.
- [`jira-roadmap-reconciliation`](skills/jira-roadmap-reconciliation/SKILL.md) -
  audits active Jira roadmap items against implementation and acceptance
  evidence, then reviews and reconciles them one item at a time.

The skills intentionally separate product-roadmap authoring from actual-work
reconciliation. The first skill does not make delivery claims or create GitHub
execution work. The second skill owns evidence classification and
implementation-state reconciliation.

## Agent Skills layout

Each skill follows the Agent Skills directory convention with progressive
disclosure:

```text
skills/<skill-name>/SKILL.md       core rules and phase routing
skills/<skill-name>/references/     detailed phase guidance, loaded on demand
```

The `SKILL.md` files contain YAML frontmatter with the skill name and
description, the core rules, and pointers to the bundled reference files. The
reference files carry the detailed phase instructions and load only when the
agent enters that phase.

## Safety model

- Provider writes require explicit approval for the current item.
- Jira and GitHub changes are preflighted, applied, and verified individually.
- Closed Jira items remain historical context unless explicitly requested for
  review.
- Credentials, authenticated URLs, and staging artifacts do not belong in this
  repository.
