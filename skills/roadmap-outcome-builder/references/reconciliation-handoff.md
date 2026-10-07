# Phase 7: Hand off to roadmap reconciliation

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
