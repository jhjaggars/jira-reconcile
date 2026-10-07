# Phase 4: Author the Jira roadmap proposal

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
