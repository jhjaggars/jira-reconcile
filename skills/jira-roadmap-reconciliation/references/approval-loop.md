# Per-item approval, submission, and verification

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

## Fresh preflight

Immediately before writing, re-read every Jira issue, link endpoint, child issue,
and subtask named in the item. Re-read every reused GitHub issue and pull
request. Confirm that repository refs, deployment state, permissions, and the
evidence class still match the proposal. Record `Approved` immediately after
approval, then record `Preflight passed` only when the reread succeeds. If the
proposal no longer matches, record `Preflight failed` or `Superseded`, preserve
the approval history, revise the item, and obtain renewed approval.

## Apply only the approved item

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

## Verify and advance

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
