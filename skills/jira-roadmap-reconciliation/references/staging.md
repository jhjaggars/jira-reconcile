# Staging file format

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
