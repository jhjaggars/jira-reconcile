---
name: jira-roadmap-reconciliation
description: >
  Audit a Jira project roadmap against repository history and current deployment
  evidence, then review and reconcile ranked issues one at a time. Stage exact
  Jira and GitHub changes, obtain approval for the current item, apply only that
  item, and verify it before advancing. Use when reviewing roadmap drift, stale
  Jira statuses, merged implementation, branch-only evidence, duplicate work,
  ranking, or Jira-to-code alignment.
---

# Jira Roadmap Reconciliation

Run an evidence-first, proposal-only audit of a Jira roadmap. The goal is to
compare the complete Jira project with what has actually happened in the
relevant repositories, then walk through the ranked roadmap one issue at a time.
The normal result is a durable staging file, not an immediate Jira write.

## Inputs

The primary project root is `$PROJECT`. Resolve it to an absolute path before
using built-in file tools. If `$PROJECT` is unset, stop and ask for the project
root rather than silently choosing a repository.

Optional inputs may be supplied in `$ARGUMENTS` or environment variables:

- `JIRA_PROJECT`: Jira project key. Infer it only when the repository or the
  user's request makes the key unambiguous.
- `JIRA_ISSUES`: optional exact Jira keys to inspect before broad ranked review.
- `OUTCOME_HANDOFF`: optional path to a locked roadmap-outcome handoff. Preserve
  its outcome revision, source artifact, live/proposed Jira state, acceptance
  targets, and unresolved questions in the reconciliation record.
- `RELATED_PROJECTS`: newline-delimited absolute repository roots. Use this for
  GitOps, deployment, infrastructure, or sibling repositories and record each
  repository's purpose.
- `GITHUB_REPOSITORIES`: owner/name values or an explicit repository-to-path
  mapping. Do not infer a GitHub repository from an unrelated local remote.
- `MAINLINE_REF`: explicit primary-repository ref, with per-repository mappings
  when related repositories use different remotes or branches.
- `STAGING_FILE`: absolute path to the durable staging artifact. Derive a path
  under `$PROJECT` only after confirming that it is private or intentionally
  committed. Do not silently create it.
- `WRITE_MODE`: `discovery-only` or `approved-item`; default to
  `discovery-only` until provider access and write intent are clear.
- `RANK_LIMIT`: optional limit for the ranked review. Do not silently truncate;
  record the limit and the issues not reviewed.

If the project references a related repository, add that repository to the
inventory using an absolute path. Do not use relative paths for additional
roots.

## Non-negotiable rules

1. **Rank is not Priority.** Use Jira Rank for roadmap order. Report Jira
   Priority separately and never substitute Priority for Rank.
2. **Mainline is the delivery boundary.** A feature is implemented only when
   the relevant commit or resulting files are reachable from the explicitly
   configured mainline ref for that repository. A branch name, remote ref, pull
   request number, or commit that is not an ancestor of that ref is not proof of
   delivery.
3. **Separate evidence classes.** Label evidence as merged mainline,
   branch-only, environment-only, specification-only, working-tree-only, or
   absent. Environment manifests do not prove successful deployment unless there
   is matching operational or qualification evidence.
4. **GitHub PR numbers are not Jira keys.** A commit message containing a
   reference such as `#158` is not evidence for a Jira key such as `PROJ-158`
   unless the Jira key is explicitly present or separately verified.
5. **Review one ranked issue at a time.** Show the current Jira fields, evidence,
   gaps, and proposed changes before moving to the next issue. Pause for the
   user's decision when the workflow is interactive.
6. **No unapproved provider writes.** Accumulate proposals in the staging file.
   Review one ranked item at a time, obtain explicit approval for that item,
   preflight it, apply only that item, verify it, and then advance. Approval of
   one item never authorizes writes to another item.
7. **Preserve exact changes.** Record old and new summary, description, status,
   Rank, Priority if changed, labels, links, parent or child relationships,
   GitHub issue or PR references, remote links, and evidence. Do not describe a
   vague intention when an exact field value is known.
8. **Never stage credentials.** Do not write tokens, cookies, passwords, private
   keys, secret values, authenticated URLs, or unclassified sensitive context to
   the staging file or evidence artifacts.
9. **Do not overclaim.** If Jira access, repository history, deployment state,
   or acceptance evidence is unavailable, say so and preserve the uncertainty.
10. **Do not reconcile terminal items by default.** Issues in a terminal Jira
    status or Done-category status are excluded from the active ranked review
    queue. Keep them as read-only historical or relationship context. Review a
    terminal item only when the user names it explicitly or an active item's
    relationship requires it for a specific decision; do not reopen or modify it
    implicitly.

## Phase 0: Configure the reconciliation run

Before collecting evidence, establish and record:

- Jira project, snapshot source, read and write permissions, and exact issue
  scope or locked-outcome handoff;
- the locked outcome baseline, its source artifact and revision, and whether the
  referenced Jira state is live or only proposed;
- local repository paths, Git remotes, and the explicit mainline ref for each
  repository;
- GitHub repository identity, issue and pull-request read access, and issue
  write access if an execution tracker is approved;
- staging destination and its data-classification decision;
- activity-window start, end, and snapshot cutoff;
- `discovery-only` versus `approved-item` write mode.

Discover the Jira capabilities needed by the proposed changes: editable field
IDs, Rank operations, issue links, remote links, transitions, resolutions,
child hierarchy, labels, and permissions. Discover the GitHub capabilities
needed for issue creation, assignment, labels, durable URLs, and read-back.
Do not assume `origin/main`, a particular Jira custom field, or a particular
link type exists across projects.

Use a capability table in the staging artifact. If a capability is unavailable,
continue with exact staged proposals where possible, identify the missing
verification, and do not claim that the provider write occurred.

## Phase 1: Collect the Jira project

Build a complete, paginated Jira snapshot before making roadmap judgments.
Use the project's configured Jira provider or approved REST integration. If the
request cannot execute because the harness blocks the network, use an existing
local cache only when its source and timestamp are known, and mark the snapshot
as stale or partial.

Collect, for every project issue:

- Jira key and numeric ID
- Summary and full rendered description, preserving the original field value
  when possible
- Issue type, status, resolution, Priority, Rank, labels, components, versions,
  assignee, reporter, created and updated timestamps
- Parent, epic, child issues, and all issue links
- Comments, changelog, status transitions, and other activity available to the
  integration
- Custom fields needed for Rank, activity type, sprint, target release, or
  project-specific workflow

Also search `$PROJECT` and related repositories for Jira keys. Repository
references are discovery hints only, not proof that an issue's work shipped.
Normalize Jira's document format into readable text for review, but retain the
raw or lossless snapshot outside the staged file when possible.

Record the Jira snapshot source, retrieval time, pagination completeness, and
any inaccessible fields in the audit notes. Do not call a partial read a
complete project snapshot.

### Jira ordering and closed-item filter

Discover which Jira statuses and status categories are terminal for this project.
Normalize the Rank field and sort only non-terminal issues by Rank. If an issue
has no Rank, put it in an explicit `Unranked` section. Keep Priority in a
separate column. If Rank is represented by a Jira custom field or rank endpoint,
record the field or operation used so a later write can preserve the old value.

Retain terminal issues in a `Closed or terminal reference set` containing their
key, status, resolution, summary, parent, children, and links. Use that set to
avoid duplicate work and preserve historical context, but do not inspect their
implementation evidence, propose status changes, rewrite their descriptions, add
new evidence links, or close/reopen them as part of the active queue.

A user-named terminal issue may be reviewed as an explicit exception. Label the
exception `Read-only terminal review` unless the user separately approves a
specific write. A closed child linked from an active issue is normally recorded
as an unchanged no-op, not re-reconciled.

## Phase 2: Collect repository history

Create a repository inventory before searching for implementation evidence.
Include:

- `$PROJECT`
- Every repository in `RELATED_PROJECTS`
- Repositories named in project documentation, remotes, submodules, manifests,
  or deployment instructions when they are relevant to the reviewed issues

For every repository, record:

- Absolute path
- Current working-tree status
- The explicit agreed mainline ref and commit
- HEAD and relevant remote refs
- Whether uncommitted changes exist
- The date and scope of the history audit

Use path-specific and topic-specific history searches, then inspect the actual
files and specifications behind promising commits. Prefer evidence such as:

- `git log --all -- <path>`
- `git log --all --grep=<term>`
- `git show --stat --summary <commit>`
- `git show <commit>:<path>`
- Current files reachable from the mainline
- Tests, generated manifests, deployment overlays, runbooks, and qualification
  results

For every implementation claim that matters to a Jira proposal, verify the
mainline boundary explicitly, for example:

```shell
git -C /absolute/repository merge-base --is-ancestor <commit> <mainline-ref>
```

Do not treat an unmerged branch, an open pull request, a remote branch name, or
an uncommitted working-tree file as merged delivery. Record branch-only evidence
because it may explain Jira activity, but label it clearly.

### Evidence ledger

Use a normalized ledger with at least these columns:

| Evidence | Repository | Ref or commit | Mainline? | Evidence class | What it proves | What it does not prove |
|---|---|---|---|---|---|---|

The final column is important. A manifest may prove intended configuration but
not a live deployment. A unit test may prove behavior in isolation but not
provider acceptance. A specification may define desired state but not delivery.

## Phase 3: Evaluate Jira against repository and Jira activity

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

## Phase 4: Reconcile the ranked items interactively

Work through the sorted Rank list of non-terminal issues. For each active issue,
present this sequence:

1. Current Jira fields and hierarchy
2. Current Jira activity or changelog signals
3. Mainline and environment evidence
4. What is implemented, partial, branch-only, or missing
5. Ownership and duplication findings
6. Proposed summary, description, status, Rank, links, and child changes
7. Exact old-value/new-value table
8. Questions or decisions that require the user

After the issue is discussed, append the decision to the staging file. Mark it
`Proposed` until that specific item is approved. Do not move to the next issue
when the user has asked to pause or has not resolved a material ambiguity.
Approval to update this issue does not approve changes to another issue, linked
item, child, GitHub tracker, or closure operation unless each operation is listed
in the approved item.

A proposal may keep a field unchanged. Record that explicitly when it matters,
for example:

| Field | Old value | New value | Action |
|---|---|---|---|
| Summary | `Trusted gateway ingress and PKI` | `Trusted gateway ingress and PKI` | Keep |
| Status | `New` | `In Progress` | Transition in approved item |
| Rank | `1|...` | `1|...` | Keep; Rank remains the roadmap order |

Do not invent an old description when Jira could not be read. Record the missing
field and provide the proposed replacement separately.

### Reconciliation item boundary

Treat one ranked issue and its explicitly listed, tightly coordinated operations
as one immutable reconciliation item. The operation list may include a Jira
field update, a curated evidence section, a Jira remote link, a Jira relationship,
a reused or newly created GitHub execution issue, and a child disposition. Each
operation must state its exact payload, expected old state, dependency, and
verification. A closure or child update is covered only when it is individually
listed in the approval request.

If evidence changes the product promise or scope rather than only the delivery
state, stop reconciliation for that item and hand it back to
`roadmap-outcome-builder` for re-locking before proposing further writes.

## Staging file format

Create or update `$STAGING_FILE` with a durable, reviewable record. It should
contain:

```markdown
# Staged Jira Roadmap Reconciliation

Status: Draft - no unapproved provider writes

## Audit baseline
- Jira project and snapshot source
- Jira snapshot completeness and timestamp
- Repository mainline names and exact baseline commits
- Related repository inventory
- GitHub repository inventory
- Closed or terminal reference set and excluded issue keys
- Activity-window boundaries and snapshot cutoff
- Evidence, access, and status limitations

## Decision ledger
| Rank | Jira | Current status | Review state | Submission state | Next action |

## [TRACKER-XXXX]
### Locked outcome baseline and handoff source
### Current Jira fields
### Current Jira activity
### Repository, GitHub, deployment, and qualification evidence
### Evidence classification ledger
### Gaps and ownership decisions
### Proposed Jira changes
### Proposed GitHub execution tracking
### Exact old-value/new-value changes
### Operation ledger
### Evidence links and remote links
### Submission state
```

Use an operation ledger for every proposed write:

```markdown
| Sequence | System | Target | Action | Exact payload or old state | Depends on | Expected response | Verification | State |
|---|---|---|---|---|---|---|---|---|
| 1 | Jira/GitHub | [key or URL] | [operation] | [non-secret payload] | [sequence] | [provider result] | [read-back assertion] | Proposed |
```

Keep the staging file free of credentials and avoid copying large raw Jira
payloads into it. Link to sanitized evidence paths, commit IDs, repository files,
GitHub URLs, and deployment records. Store raw snapshots outside the repository
or in an explicitly approved artifact location.

## Per-item approval, submission, and verification

Use a sequential review loop by default. For the next ranked item, show:

1. Current Jira fields, hierarchy, and relevant changelog activity.
2. Repository, GitHub, deployment, and qualification evidence with its evidence
   class and mainline status.
3. What is implemented, partial, branch-only, environment-only, specification-
   only, absent, or acceptance-pending.
4. Proposed Jira field, description, status, Rank, link, hierarchy, and child
   changes.
5. Proposed GitHub tracker creation or update, evidence section, remote link,
   comment, or closure operation.
6. Exact old-value/new-value and operation-ledger entries.
7. Fields, relationships, children, and items that remain unchanged.
8. Unresolved questions, stale evidence, and the next item to be reviewed.

Ask for explicit approval of this item only. Approval must identify whether it
covers Jira writes, GitHub writes, remote links, relationship changes, and child
or closure operations. Approval of one item never authorizes writes to another
item. This workflow does not use implicit full-batch approval. Each ranked item
requires its own approval, preflight, apply, and verification sequence.

### Fresh preflight

Immediately before writing, re-read every Jira issue, link endpoint, child issue,
and subtask named in the item. Re-read every reused GitHub issue and PR. Confirm
that repository refs, deployment state, permissions, and the evidence class
still match the proposal. Record `Approved` immediately after approval, then
record `Preflight passed` only when the reread succeeds. If the proposal no
longer matches, record `Preflight failed` or `Superseded`, preserve the approval
history, revise the item, and obtain renewed approval.

### Apply only the approved item

1. Follow the operation ledger's dependency order. Create a Jira Epic or capture
   its key before creating a GitHub issue that must include that key, or create
   the GitHub issue first when the Jira payload does not depend on its URL.
2. Create or update only the approved GitHub issue using the exact staged title,
   body, labels, assignee, and repository. Do not duplicate an existing issue.
3. Apply the approved Jira description, evidence section, fields, Jira links,
   remote links, comments, and status or Rank operations using discovered field
   IDs and workflow values.
4. Apply approved child or linked-item operations. For a closure, use the actual
   discovered transition and resolution and include the approved evidence or
   closure comment.
5. Do not write to the next ranked item while applying the current item.

If a provider partially succeeds, stop the sequence, record every confirmed and
unconfirmed operation, mark the item `Partially submitted`, and reconcile it
before continuing.

### Verify and advance

1. Re-read every changed Jira issue, relationship, child, and subtask.
2. Re-read every created or updated GitHub issue and confirm its URL, state,
   title, body, labels, assignee, and Jira-key reference.
3. Confirm the final evidence classification, description references, remote
   links, status, Rank, and resolution against the approved ledger.
4. Record adapter response identifiers or status codes and verification results.
5. Mark the item `Submitted` only after provider responses succeed and `Verified`
   only after all expected read-back assertions pass.
6. Summarize what changed and remained unchanged, then present the next ranked
   item and wait for separate approval before writing it.

Never claim that a Jira or GitHub update was submitted without a successful
provider response. If write access is unavailable, remain in discovery-only
mode, provide exact payloads, and record the missing verification.

## Resume and stop conditions

The staging file is the phase boundary. A later session must be able to resume
from it without depending on the chat transcript. Before stopping, record:

- Jira snapshot source and freshness
- Repository baseline commits
- Issues reviewed and their decisions
- The next Rank to review
- Open design, ownership, target-environment, and access questions
- The current item's approval, preflight, submission, and verification state
- Whether any Jira or GitHub changes were submitted

Stop and ask the user when:

- The Jira project or Rank source is ambiguous
- The relevant mainline cannot be identified
- A proposed status or architecture change depends on an unresolved decision
- The user has not approved moving to the next issue
- Jira or GitHub writes are requested before the current item is approved
