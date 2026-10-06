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

## Phase 0: Configure the roadmap run

Before shaping outcomes or planning writes, establish the Jira and staging
boundary. Record these decisions in the staging artifact when one is available,
or in the conversation until a safe artifact is approved:

- Jira project and exact candidate keys;
- Jira read access and whether Jira write access is available;
- supported Jira issue types, Epic fields, editable field IDs, link types,
  child hierarchy, transitions, resolutions, permissions, and project labels;
- `discovery-only` versus `approved-item` write mode;
- staging destination and its data-classification decision.

Discover the Jira capabilities needed by the proposed operations. Do not assume
that every project exposes `Epic Name`, Rank, activity type, remote links, child
hierarchy, or the same status and resolution values. Map logical fields such as
`Epic Name`, `roadmap rank`, and `team label` to the fields this project actually
supports.

Use a capability table like this:

| System | Capability | Available | Adapter or evidence | Constraint |
|---|---|---|---|---|
| Jira | Read issues and changelog | yes/no | [adapter or source] | [scope] |
| Jira | Edit fields and descriptions | yes/no | [adapter or source] | [scope] |
| Jira | Create issue links | yes/no | [adapter or source] | [scope] |
| Jira | Create or edit Epics | yes/no | [adapter or source] | [scope] |
| Staging | Approved private destination | yes/no | [path and classification] | [scope] |

If Jira read access is unavailable, remain in conversation or proposal mode and
do not invent current values. If Jira write access is unavailable, produce exact
staged payloads and say that no Jira change was confirmed. If no safe staging
destination is available, keep the artifact in the conversation and do not enter
Phase 5 or Phase 6.

## Phase 1: Capture the informal list

Accept fragments, shorthand, internal terminology, and incomplete ideas without
prematurely turning them into tickets. Normalize the list only enough to identify
candidate outcomes.

For each candidate, write:

```text
Working outcome: [what users or operators will be able to do]
Likely value: [why it matters]
Unknowns: [the smallest questions that change the scope]
```

Group duplicate or overlapping bullets, but do not merge distinct outcomes just
because they share infrastructure. External secret storage, user access,
confidential workloads, and gateway upgrades may share implementation machinery
while remaining separate roadmap outcomes.

## Phase 2: Refine one outcome at a time

Use this structure for each candidate.

### Outcome title

Use a concise product statement:

- `Enable existing SSO users to access managed gateways`
- `Offer confidential-container hosting for managed workloads`
- `Provide managed lifecycle upgrades for gateways`

Avoid implementation-only titles such as `Add a secret-manager adapter` or
`Implement a runtime class` unless those are genuinely the user-visible outcome.

### Press-release statement

Write one paragraph answering:

- Who benefits?
- What can they do now?
- What problem or adoption blocker is removed?
- What is intentionally not promised?

### User workflow

Describe the visible sequence from the user's perspective. Keep implementation
behind the service boundary when users do not choose it. If the service configures
an external provider, do not present the provider choice as a user feature unless
users actually select it.

### Included scope

List the capabilities required for the outcome to be real. Include operational
behavior when it materially affects the promise:

- security and authorization;
- lifecycle and recovery;
- observability;
- supported environments;
- migration or non-migration behavior;
- documentation and operator workflow.

### Explicit exclusions

State what the outcome does not include. Typical exclusions include:

- existing resources are not migrated;
- users do not choose an implementation strategy;
- automatic RBAC is not included;
- a control plane is not duplicated;
- zero downtime is not promised yet;
- a technology is supported only where the upstream project supports it.

### Completion signal

Write a testable statement of what must be true before the outcome can be
announced. This is a product acceptance target, not evidence that the target has
already been met. Reconciliation later determines whether the target is met.

### Refinement questions

Ask only the questions that change the outcome:

- Which exact object or user is in scope?
- Is the capability for new resources, existing resources, or both?
- Who operates the feature and who selects it?
- What happens when a prerequisite is unavailable?
- Is a manual step acceptable, or must the service automate it?
- What is the minimum initial release versus the ideal future state?
- Which availability or compatibility promises are still unknown?

Do not move to the next outcome until the user confirms that the current one is
accurate. Record the locked wording in the staging artifact or a roadmap draft.

## Phase 3: Map locked outcomes to the current Jira roadmap

After outcomes are locked, collect the current Jira records for the project or
exact candidate keys. This is a roadmap-structure inspection, not a delivery
audit. Read at least:

- summary, Epic Name or equivalent, and full description;
- issue type, status, resolution, Priority, and Rank;
- assignee, labels, components, activity type, and timestamps;
- parent and children;
- issue links, comments, and relevant changelog activity.

Do not search repository history, GitHub, deployment systems, or current files in
this phase. If a Jira description contains a GitHub URL or implementation claim,
record it as existing Jira text only. Do not verify or promote it to evidence.

Do not assume Jira has a `Feature` issue type. Query project metadata. If the
project maps roadmap outcomes to `Epic`, use `Epic`; otherwise use the project's
supported roadmap issue type and record the mapping.

Classify each current issue as:

- direct match that needs rewriting;
- partial match or enabling roadmap work;
- unrelated supporting capability;
- duplicate or stale wording;
- historical closed or `Won't Do` issue;
- no existing issue, requiring a new Epic or supported roadmap item.

For a direct match, show:

1. Current Jira fields and hierarchy.
2. The mismatch with the locked outcome.
3. Proposed summary and description.
4. Exact unchanged and changed fields.
5. Current Status and ownership, explicitly marked unchanged unless a separate
   product decision requires a planning-state change. Do not recommend a delivery
   transition from product shaping alone.
6. Questions that must be answered by reconciliation later.

Do not reopen a historical `Won't Do` Epic merely because the new outcome sounds
related. Prefer a new Epic when repurposing would obscure history. Do not
automatically close, merge, or move related Epics.

### Jira link and child disposition

For every relevant Epic, propose a roadmap disposition:

- keep the Epic and update its summary, description, Rank, Priority, labels,
  assignee, or activity type;
- keep the Epic unchanged because it is supporting or unrelated;
- link the Epic to another outcome or supporting Epic;
- split or replace the Epic when it combines distinct outcomes;
- leave a historical closed or `Won't Do` Epic unchanged.

Treat these as different relationships:

- parent and child relationships;
- Jira issue links such as `relates to`, `blocks`, `is blocked by`, or
  `duplicates`;
- Jira `Sub-task` issues and other implementation children.

For every proposed link, record exact source key, target key, link type,
direction, and product rationale. Do not infer a link from a shared keyword or
technology. Do not remove, reverse, or replace an existing link without an
explicit product decision.

For children or linked items, record one of these roadmap dispositions:

- retain unchanged;
- update summary or product scope;
- move to another parent or outcome;
- link to a supporting Epic;
- split or replace;
- defer to reconciliation for evidence-based disposition.

Do not classify an item as complete, duplicate, obsolete, or `Won't Do` here
unless the user makes that explicit product decision independently of repository
or deployment evidence. If implementation evidence is needed, hand the item to
reconciliation.

## Phase 4: Author the Jira roadmap proposal

For each locked outcome, prepare the customer-facing Jira representation:

- summary and Epic Name or project-specific equivalent;
- press-release statement and user workflow;
- included scope and explicit exclusions;
- completion signal and initial acceptance criteria;
- dependencies and unresolved product decisions;
- approved Jira relationships and hierarchy;
- proposed status, Rank, Priority, labels, assignee, and activity type;
- fields and relationships intentionally left unchanged.

Jira Status is reserved for the project's delivery workflow. Preserve the
existing Status during roadmap authoring. A newly created Epic uses the Jira
provider default unless the user explicitly approves a planning-state choice
that the project has defined independently of implementation evidence. Do not
infer or transition Status from a branch, pull request, commit, deployment, or
issue assignment. Evidence-based Status recommendations belong to
`jira-roadmap-reconciliation`.

Jira child Stories may be proposed only when Jira hierarchy, ownership, reporting,
or completion criteria explicitly require them. A child Story in this workflow is
a roadmap decomposition, not a mirror of a GitHub issue. Stage it with:

```markdown
### Child item: [summary]
- Parent: [Jira key]
- Issue type: [discovered type]
- Current state: [or new]
- Proposed fields: [exact values]
- Description: [exact product text]
- Acceptance criteria: [testable target]
- Evidence status: Reconciliation required
- Operation state: Proposed
```

Do not create GitHub issues, add GitHub URLs, add delivery evidence sections, or
close children as part of this proposal. Those are reconciliation operations.

## Phase 5: Stage exact Jira proposals

Use `$STAGING_FILE` as the durable phase boundary. It must record:

- Jira project, snapshot source, retrieval time, and completeness;
- each outcome's locked wording and unresolved questions;
- current Jira fields and exact old values;
- exact proposed new values and full descriptions;
- proposed Jira links with exact type and direction;
- proposed parent, child, and subtask dispositions;
- product rationale for unchanged or historical items;
- reconciliation handoff notes and acceptance targets;
- skipped or unrelated issues;
- submission state;
- an operation ledger for every proposed Jira write.

Use this structure:

```markdown
# Staged Jira Roadmap Updates

Status: Draft - not submitted to Jira

## Roadmap baseline
- Jira project and snapshot source
- Snapshot completeness and timestamp
- Jira capability limitations
- Staging data-classification decision

## Outcome ledger
| Sequence | Outcome | Jira key | Locked? | Roadmap decision | Submission state |
|---|---|---|---|---|---|

## [TRACKER-XXXX]
### Locked outcome
### Current Jira fields
### Current Jira description
### Alignment decision
### Proposed Jira changes
### Exact old-value/new-value changes
### Proposed links and hierarchy
### Unchanged fields and relationships
### Reconciliation handoff
### Submission state
```

For every Jira write, include:

```markdown
| Sequence | Target | Action | Exact payload or old state | Depends on | Expected response | Verification | State |
|---|---|---|---|---|---|---|---|
| 1 | [Jira key or placeholder] | [operation] | [non-secret payload] | [sequence] | [provider result] | [read-back assertion] | Proposed |
```

The ledger must include field updates, issue links, transitions, comments,
child operations, and any closure operation explicitly approved as a product
decision. Do not leave material payload choices to the apply phase.

Use these submission states when useful:

```text
Proposed -> Approved -> Preflight passed -> Submitted -> Verified
```

Also record `Preflight failed`, `Partially submitted`, `Deferred`, `Skipped`,
`Superseded`, or `Failed` when applicable.

Do not put raw authenticated payloads, credentials, or unclassified sensitive
content in the staging file.

## Phase 6: Review, approve, apply, and verify one Jira item at a time

Use a sequential review loop by default. A roadmap **item** is one immutable,
enumerated Jira operation set for one Epic or supported roadmap item. Every
field update, relationship, child operation, comment, transition, or closure
must be listed separately in the operation ledger.

### Present the next item

For the next unreviewed item, show:

1. Current Jira fields and hierarchy.
2. Locked outcome and the mismatch it addresses.
3. Every exact field, description, status, Rank, Priority, label, assignee,
   relationship, parent, child, and comment change.
4. Product rationale for each change.
5. Every field, relationship, child, and item that remains unchanged.
6. Reconciliation questions that are intentionally deferred.
7. The next item that will be considered after this one.

Ask for explicit approval of this Jira item only. Approval to update an Epic does
not implicitly approve closing or modifying unrelated linked items or children.
Approval also does not approve any future GitHub or reconciliation write.

### Fresh preflight after approval

Immediately before writing, re-read every Jira issue, link endpoint, child issue,
and subtask named in the approved item. Abort before writing if any reviewed old
value, relationship, permission, or workflow capability changed unexpectedly.
If the proposal no longer matches, record the new current state and present the
revised item for approval instead of silently adapting it.

Record `Approved` immediately after user approval, including the approved
operation list. On a successful preflight, add `Preflight passed`. On mismatch,
retain the approval history, record `Preflight failed` or `Superseded`, and obtain
renewed approval for the revised operation set.

### Apply only the approved Jira item

1. Follow the operation ledger's dependency order.
2. Apply only approved Jira field, description, relationship, hierarchy,
   transition, comment, or closure operations.
3. Use the discovered Jira link type, transition, resolution, and field IDs.
4. Use Jira Rank operations for Rank, not the Priority field.
5. Leave new Epics unassigned when that is the approved state.
6. Do not write to the next item while applying the current item.

If a provider partially succeeds, stop the sequence, record every confirmed and
unconfirmed operation, mark the item `Partially submitted`, and reconcile the
current item before continuing.

### Verify and advance

1. Re-read every changed Jira issue, relationship, child, and subtask.
2. Confirm final fields, descriptions, links, hierarchy, status, and resolution
   against the approved operation ledger.
3. Record provider response identifiers or status codes and verification results
   in the staging file.
4. Mark the item `Submitted` only after the expected provider responses succeed
   and `Verified` only after the read-back state matches.
5. Summarize what changed and remained unchanged, then present the next item and
   wait for separate approval before writing it.

If the user defers or skips an item, record `Deferred` or `Skipped` with the
reason and do not write it. If Jira access is unavailable, provide exact staged
payloads and do not claim submission.

## Phase 7: Hand off to roadmap reconciliation

Once an outcome is locked and its Jira roadmap proposal is staged or verified,
prepare a handoff for `jira-roadmap-reconciliation`. The handoff must contain:

- the locked outcome revision or sequence identifier;
- the source staging artifact path and revision, timestamp, or content marker;
- the Jira key or durable new-item placeholder, explicitly marked as live or
  proposed state;
- locked summary, Epic Name, description, scope, exclusions, and completion
  signal;
- approved Jira hierarchy and relationships;
- current Jira Status, marked as live state and not endorsed as delivery
  evidence; any proposed Rank or other roadmap-order decision;
- each approved Jira operation and its state, such as `Proposed`, `Submitted`,
  or `Verified`;
- unresolved product, ownership, environment, or dependency questions;
- the exact acceptance targets that repository, GitHub, deployment, or
  qualification evidence must later test;
- an explicit statement that implementation and delivery evidence has not been
  assessed by this skill.

Use this handoff format:

```markdown
## Reconciliation handoff: [Jira key or placeholder]
### Source staging artifact and outcome revision
### Jira roadmap state at handoff: Live or Proposed
### Locked outcome
### Included scope and exclusions
### Acceptance targets
### Approved Jira operations and states
### Unresolved questions
### Evidence still required
### Reconciliation state: Not started
```

Invoke `jira-roadmap-reconciliation` when the user wants to compare actual work
against the roadmap. Do not duplicate its repository and GitHub audit here.
Reconciliation owns:

- Jira project snapshots and ranked issue review;
- repository and mainline evidence;
- GitHub issue and pull-request evidence;
- deployment and qualification evidence;
- status, Rank, ownership, and stale-scope recommendations based on evidence;
- curated GitHub evidence links and execution trackers;
- evidence-based child and linked-item dispositions;
- returning an outcome to this skill when evidence changes the product promise
  or scope.

If reconciliation shows that the locked outcome is inaccurate, return to Phase 2,
revise and re-lock the outcome with the user, invalidate affected unstaged
proposals, and repeat the roadmap mapping before writing further Jira changes.

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
