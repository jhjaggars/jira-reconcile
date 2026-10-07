# Phase 5: Stage exact Jira proposals

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
