# Phase 1: Collect the Jira project

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

## Jira ordering and closed-item filter

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
