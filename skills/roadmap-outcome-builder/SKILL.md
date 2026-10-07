---
name: roadmap-outcome-builder
description: >
  Turn informal product goals into press-release-sized roadmap outcomes, refine
  them one at a time, map them to Jira Epics, stage exact Jira roadmap changes,
  and hand locked outcomes to jira-roadmap-reconciliation for implementation and
  delivery evidence. Use when a team needs a customer-facing roadmap rather
  than an implementation audit or a list of technical tasks.
---

# Roadmap Outcome Builder

This skill owns **Workflow A: idea to roadmap**.

It turns stakeholder ideas into locked, customer-facing outcomes and then
creates or updates the Jira roadmap representation for those outcomes. It may
inspect current Jira roadmap records to avoid duplicates and preserve history,
but it does not determine whether implementation shipped.

**Workflow B: reconcile actual work against the roadmap** belongs to
`jira-roadmap-reconciliation`. That workflow owns repository history, GitHub
activity, deployment evidence, mainline reachability, stale status analysis,
GitHub execution tracking, evidence links, child-item closure, and delivery
claims.

| Workflow | Primary question | Evidence source | Primary output |
|---|---|---|---|
| Idea to roadmap | What customer-facing outcomes should the roadmap represent? | Stakeholder decisions and current Jira roadmap structure | Locked outcomes and approved Jira Epic proposals or updates |
| Roadmap reconciliation | What has actually been built, merged, deployed, or accepted? | Jira activity, Git history, GitHub, deployment, and qualification evidence | Evidence-backed Jira updates and execution-tracking decisions |

Do not use this skill to claim that a feature is implemented, to classify a
commit or pull request as delivery, to add GitHub evidence links, or to close a
Jira item because repository work appears complete. Hand those questions to
`jira-roadmap-reconciliation`.

## Inputs

Optional inputs may be supplied through `$ARGUMENTS` or environment variables:

- `JIRA_PROJECT`: Jira project key. Infer only when unambiguous.
- `JIRA_ISSUES`: optional exact Jira keys to inspect before a broader roadmap
  search.
- `PROJECT`: absolute path used only for a local staging artifact or roadmap
  documentation. Do not silently select a repository.
- `STAGING_FILE`: absolute path to the durable staging artifact. If omitted,
  derive `$PROJECT/docs/jira-roadmap-staged-updates.md` only after `PROJECT` is
  set and the user confirms that the destination is private or intentionally
  committed.
- `WRITE_MODE`: `discovery-only` or `approved-item`; default to
  `discovery-only` until Jira access and the user's write intent are clear.
- `TEAM_LABEL`: optional project label to propose when the Jira project
  convention and discovered field permissions support it.

This skill does not require `RELATED_PROJECTS`, `MAINLINE_REF`,
`GITHUB_REPOSITORIES`, or an activity window. Those belong to the reconciliation
workflow. If the user supplies repository or GitHub evidence while shaping an
outcome, record it as an unverified question or handoff input rather than
turning it into delivery evidence here.

Resolve any repository path used for staging to an absolute path. Do not create
or modify a staging artifact until the user has supplied `STAGING_FILE` or
confirmed the derived destination.

## Core rules

1. **Start with outcomes, not Jira.** First shape the stakeholder's ideas into
   customer-facing outcomes. Do not create tickets from raw bullets.
2. **One outcome at a time.** Present a working outcome, ask focused refinement
   questions, lock it, and only then move to the next outcome.
3. **Do not headline implementation technology.** Secret managers, confidential
   runtimes, identity providers, Helm, and similar technologies belong in scope
   or implementation notes unless the technology itself is the user-facing value.
4. **Use explicit boundaries.** Every outcome needs included scope, exclusions,
   completion signals, dependencies, and unresolved questions.
5. **Do not invent product behavior.** Preserve distinctions such as possible,
   optional, future, manual, unsupported, or out of scope.
6. **Jira Rank is not Priority.** Rank controls roadmap order. Priority is a
   separate Jira field and must not be substituted for Rank.
7. **Do not make delivery claims.** A GitHub reference such as `#123`, a Jira
   key, a branch name, or a description containing implementation language is
   not delivery evidence in this workflow. Reconciliation verifies such claims.
8. **No unapproved Jira writes.** Stage proposals while the roadmap is being
   shaped. Review and obtain explicit approval for one Jira Epic or update item
   at a time, apply only that approved item, verify it, record the result, and
   then move to the next item.
9. **Preserve exact values.** Record old and new summary, Epic Name or its
   project-specific equivalent, description, issue type, status, resolution,
   Rank or roadmap-order field, Priority, labels, components, activity type,
   assignee, links, parent, and child relationships. Mark unavailable or
   read-only fields explicitly. This workflow does not transition an existing
   Jira Status based on product shaping; reconciliation owns evidence-based
   delivery-status transitions. A new Epic uses the provider's default status
   unless the user explicitly approves a planning-state choice.
10. **Protect the staging artifact.** Do not write tokens, cookies, passwords,
    private keys, secret values, authenticated URLs, or unclassified customer
    or operational data to it. Confirm that its destination is private or
    approved for repository tracking before copying full Jira text.
11. **Do not force alignment.** An existing Epic may be unrelated, supporting,
    stale, duplicate, or historical `Won't Do` work. Leave it alone when that is
    the correct roadmap decision.
12. **Propose hierarchy changes explicitly.** Record exact Jira link types,
    directions, parent relationships, child dispositions, and the rationale.
13. **Do not close based on this workflow.** A child or linked issue may be
    proposed for a future disposition, but completion, duplicate, obsolete, and
    `Won't Do` closure decisions that depend on actual work belong to
    reconciliation unless the user makes an explicit product decision unrelated
    to implementation evidence.
14. **Do not create GitHub work as a side effect.** This skill may record a
    future reconciliation question, but it does not create, update, assign, or
    link GitHub issues or pull requests.
15. **Keep roadmap and execution boundaries clear.** Jira Epics represent the
    approved customer-facing outcome. Repository implementation, E2E,
    qualification, and deployment work are reconciled separately.

## Workflow phases

Work through these phases in order. Each phase's detailed instructions live in
a bundled reference file; read it when entering the phase.

| Phase | Purpose | Reference |
|---|---|---|
| 0. Configure the roadmap run | Establish Jira and staging boundaries and capabilities | [references/run-configuration.md](references/run-configuration.md) |
| 1. Capture the informal list | Normalize stakeholder fragments into candidate outcomes | [references/outcome-refinement.md](references/outcome-refinement.md) |
| 2. Refine one outcome at a time | Shape and lock each outcome with the user | [references/outcome-refinement.md](references/outcome-refinement.md) |
| 3. Map locked outcomes to the current Jira roadmap | Classify current issues and propose dispositions | [references/jira-mapping.md](references/jira-mapping.md) |
| 4. Author the Jira roadmap proposal | Write the customer-facing Jira representation | [references/proposal-authoring.md](references/proposal-authoring.md) |
| 5. Stage exact Jira proposals | Record proposals in the durable staging artifact | [references/staging.md](references/staging.md) |
| 6. Review, approve, apply, and verify one item at a time | Sequential Jira writes with preflight and verification | [references/approval-loop.md](references/approval-loop.md) |
| 7. Hand off to roadmap reconciliation | Produce the reconciliation handoff | [references/reconciliation-handoff.md](references/reconciliation-handoff.md) |

Phase summary:

- **Phase 0** records the Jira project, access, write mode, staging destination,
  and a capability table mapping logical fields to what the project supports.
- **Phases 1-2** turn fragments into locked outcomes with a title, press-release
  statement, user workflow, included scope, exclusions, completion signal, and
  refinement questions.
- **Phase 3** inspects current Jira records as roadmap structure only, never as
  delivery evidence, and proposes per-Epic and per-child dispositions.
- **Phase 4** writes the proposal. Jira Status is preserved; a new Epic uses the
  provider default unless the user explicitly approves a planning-state choice.
- **Phase 5** records every proposal, old value, payload, dependency, and
  verification assertion in an operation ledger.
- **Phase 6** approves, preflights, applies, and verifies exactly one item at a
  time.
- **Phase 7** hands the locked outcome, Jira state, and acceptance targets to
  `jira-roadmap-reconciliation`. If reconciliation shows the locked outcome is
  inaccurate, return to Phase 2, re-lock with the user, invalidate affected
  unstaged proposals, and repeat the mapping.

## Stop conditions

Stop and ask the user when:

- the product promise, audience, or initial scope is ambiguous;
- the Jira project or roadmap issue type is ambiguous;
- the current Jira record cannot be read and an exact old value is required;
- a historical `Won't Do` item would need repurposing;
- a hierarchy, status, Rank, ownership, or closure decision is unresolved;
- the staging destination is not approved or safe;
- the user has not approved moving to the next Jira item;
- the request has become an implementation or delivery audit.

When the request becomes an implementation or delivery audit, stop this workflow
and recommend `jira-roadmap-reconciliation` rather than expanding this skill.
