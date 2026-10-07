# Phase 6: Review, approve, apply, and verify one Jira item at a time

Use a sequential review loop by default. A roadmap **item** is one immutable,
enumerated Jira operation set for one Epic or supported roadmap item. Every
field update, relationship, child operation, comment, transition, or closure
must be listed separately in the operation ledger.

## Present the next item

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

## Fresh preflight after approval

Immediately before writing, re-read every Jira issue, link endpoint, child issue,
and subtask named in the approved item. Abort before writing if any reviewed old
value, relationship, permission, or workflow capability changed unexpectedly.
If the proposal no longer matches, record the new current state and present the
revised item for approval instead of silently adapting it.

Record `Approved` immediately after user approval, including the approved
operation list. On a successful preflight, add `Preflight passed`. On mismatch,
retain the approval history, record `Preflight failed` or `Superseded`, and obtain
renewed approval for the revised operation set.

## Apply only the approved Jira item

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

## Verify and advance

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
