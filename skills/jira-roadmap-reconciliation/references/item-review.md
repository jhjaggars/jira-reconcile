# Phase 4: Reconcile the ranked items interactively

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

## Reconciliation item boundary

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
