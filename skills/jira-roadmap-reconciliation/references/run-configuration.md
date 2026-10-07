# Phase 0: Configure the reconciliation run

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
continue with exact staged proposals and a recorded gap, identify the missing
verification, and do not claim that the provider write occurred.
