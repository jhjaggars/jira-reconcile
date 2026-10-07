# Phase 3: Evaluate Jira against repository and Jira activity

For each ranked issue, compare three sources:

1. **Jira current state:** summary, description, status, Rank, Priority, children,
   links, and changelog.
2. **Repository history:** merged commits, current mainline files, tests,
   generated output, and deployment changes.
3. **Operational state:** deployment or qualification evidence, when available.

Classify each active issue and its active children using one or more of the
following **evidence assessments**. Do not run this assessment for the terminal
reference set unless the issue was explicitly requested as an exception. These
labels describe what the evidence shows; they are not automatic Jira Status
transitions. Map an assessment to the project's actual workflow only in an
approved reconciliation item.

- `Not started`: no credible implementation evidence
- `Refinement`: scoped or designed, but not active delivery
- `In Progress`: substantial work is merged or actively underway, but acceptance
  is incomplete
- `Partially delivered`: some acceptance criteria are proven and others remain
- `Implemented, acceptance pending`: code is merged but deployment or operational
  evidence is missing
- `Environment-only`: a deployment exists without reusable mainline capability
- `Branch-only`: relevant work exists outside the agreed mainline
- `Stale or duplicate`: Jira wording no longer matches current architecture or
  overlaps another issue
- `Complete`: implementation and the issue's acceptance evidence are both
  sufficient for closure

Do not change a Jira status solely because a commit contains a matching ticket
number. Check the actual scope and acceptance criteria. Conversely, do not leave
an issue marked `New` when the mainline clearly contains substantial work, unless
its remaining acceptance boundary justifies keeping it `New`.

Look specifically for:

- Jira issues marked `New` or `Refinement` whose implementation is already in
  mainline
- Active issues whose promised behavior is absent from mainline or lacks the
  required acceptance evidence. Terminal issues remain in the reference set
  unless the user explicitly requested a read-only exception review.
- Parent epics whose children duplicate current architecture or overlap another
  parent
- Children that are implemented in code but still need provider, production,
  recovery, security, or end-to-end qualification
- Environment-specific work incorrectly presented as reusable product delivery
- Dependencies that point to stale summaries or the wrong ownership boundary
- Issues whose Rank no longer reflects the current delivery sequence

Current code and later approved architecture decisions win for delivered
behavior and evidence classification, not for the customer-facing product
promise. If implementation diverges from the locked summary, scope, exclusions,
or acceptance targets, record a scope conflict and hand the outcome back to
`roadmap-outcome-builder` for re-locking. Reconciliation may propose corrections
to delivery status, evidence, links, ownership, or technical references that do
not change the promise, but must not rewrite roadmap scope silently.
