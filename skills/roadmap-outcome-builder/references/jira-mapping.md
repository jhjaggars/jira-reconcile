# Phase 3: Map locked outcomes to the current Jira roadmap

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

## Jira link and child disposition

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
