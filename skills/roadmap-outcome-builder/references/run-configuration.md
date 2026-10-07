# Phase 0: Configure the roadmap run

Before shaping outcomes or planning writes, establish the Jira and staging
boundary. Record these decisions in the staging artifact when one is available,
or in the conversation until a safe artifact is approved:

- Jira project and exact candidate keys;
- Jira read access and whether Jira write access is available;
- supported Jira issue types, Epic fields, editable field IDs, link types,
  child hierarchy, transitions, resolutions, permissions, and project labels;
- `discovery-only` versus `approved-item` write mode;
- staging destination and its data-classification decision.

Discover the Jira capabilities needed by the proposed operations. Do not assume
that every project exposes `Epic Name`, Rank, activity type, remote links, child
hierarchy, or the same status and resolution values. Map logical fields such as
`Epic Name`, `roadmap rank`, and `team label` to the fields this project actually
supports.

Use a capability table like this:

| System | Capability | Available | Adapter or evidence | Constraint |
|---|---|---|---|---|
| Jira | Read issues and changelog | yes/no | [adapter or source] | [scope] |
| Jira | Edit fields and descriptions | yes/no | [adapter or source] | [scope] |
| Jira | Create issue links | yes/no | [adapter or source] | [scope] |
| Jira | Create or edit Epics | yes/no | [adapter or source] | [scope] |
| Staging | Approved private destination | yes/no | [path and classification] | [scope] |

If Jira read access is unavailable, remain in conversation or proposal mode and
do not invent current values. If Jira write access is unavailable, produce exact
staged payloads and say that no Jira change was confirmed. If no safe staging
destination is available, keep the artifact in the conversation and do not enter
Phase 5 or Phase 6.
