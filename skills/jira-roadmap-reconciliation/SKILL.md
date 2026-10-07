---
name: jira-roadmap-reconciliation
description: >
  Audit a Jira project roadmap against repository history and current deployment
  evidence, then review and reconcile ranked issues one at a time. Stage exact
  Jira and GitHub changes, obtain approval for the current item, apply only that
  item, and verify it before advancing. Use when reviewing roadmap drift, stale
  Jira statuses, merged implementation, branch-only evidence, duplicate work,
  ranking, or Jira-to-code alignment.
---

# Jira Roadmap Reconciliation

Run an evidence-first, proposal-only audit of a Jira roadmap. The goal is to
compare the complete Jira project with what has actually happened in the
relevant repositories, then walk through the ranked roadmap one issue at a time.
The normal result is a durable staging file, not an immediate Jira write.

This skill owns **Workflow B: reconcile actual work against the roadmap**.
Product-outcome shaping and Jira roadmap authoring belong to
`roadmap-outcome-builder`. If evidence changes a product promise or scope rather
than only the delivery state, stop and hand the outcome back to that skill for
re-locking.

## Inputs

The primary project root is `$PROJECT`. Resolve it to an absolute path before
using built-in file tools. If `$PROJECT` is unset, stop and ask for the project
root rather than silently choosing a repository.

Optional inputs may be supplied in `$ARGUMENTS` or environment variables:

- `JIRA_PROJECT`: Jira project key. Infer it only when the repository or the
  user's request makes the key unambiguous.
- `JIRA_ISSUES`: optional exact Jira keys to inspect before broad ranked review.
- `OUTCOME_HANDOFF`: optional path to a locked roadmap-outcome handoff. Preserve
  its outcome revision, source artifact, live/proposed Jira state, acceptance
  targets, and unresolved questions in the reconciliation record.
- `RELATED_PROJECTS`: newline-delimited absolute repository roots. Use this for
  GitOps, deployment, infrastructure, or sibling repositories and record each
  repository's purpose.
- `GITHUB_REPOSITORIES`: owner/name values or an explicit repository-to-path
  mapping. Do not infer a GitHub repository from an unrelated local remote.
- `MAINLINE_REF`: explicit primary-repository ref, with per-repository mappings
  when related repositories use different remotes or branches.
- `STAGING_FILE`: absolute path to the durable staging artifact. Derive a path
  under `$PROJECT` only after confirming that it is private or intentionally
  committed. Do not silently create it.
- `WRITE_MODE`: `discovery-only` or `approved-item`; default to
  `discovery-only` until provider access and write intent are clear.
- `RANK_LIMIT`: optional limit for the ranked review. Do not silently truncate;
  record the limit and the issues not reviewed.

If the project references a related repository, add that repository to the
inventory using an absolute path. Do not use relative paths for additional
roots.

## Non-negotiable rules

1. **Rank is not Priority.** Use Jira Rank for roadmap order. Report Jira
   Priority separately and never substitute Priority for Rank.
2. **Mainline is the delivery boundary.** A feature is implemented only when
   the relevant commit or resulting files are reachable from the explicitly
   configured mainline ref for that repository. A branch name, remote ref, pull
   request number, or commit that is not an ancestor of that ref is not proof of
   delivery.
3. **Separate evidence classes.** Label evidence as merged mainline,
   branch-only, environment-only, specification-only, working-tree-only, or
   absent. Environment manifests do not prove successful deployment unless there
   is matching operational or qualification evidence.
4. **GitHub pull-request numbers are not Jira keys.** A commit message
   containing a reference such as `#158` is not evidence for a Jira key such as
   `PROJ-158` unless the Jira key is explicitly present or separately verified.
5. **Review one ranked issue at a time.** Show the current Jira fields,
   evidence, gaps, and proposed changes before moving to the next issue. Pause
   for the user's decision when the workflow is interactive.
6. **No unapproved provider writes.** Accumulate proposals in the staging file.
   Review one ranked item at a time, obtain explicit approval for that item,
   preflight it, apply only that item, verify it, and then advance. Approval of
   one item never authorizes writes to another item.
7. **Preserve exact changes.** Record old and new summary, description, status,
   Rank, Priority if changed, labels, links, parent or child relationships,
   GitHub issue or pull-request references, remote links, and evidence. Do not
   describe a vague intention when an exact field value is known.
8. **Never stage credentials.** Do not write tokens, cookies, passwords,
   private keys, secret values, authenticated URLs, or unclassified sensitive
   context to the staging file or evidence artifacts.
9. **Do not overclaim.** If Jira access, repository history, deployment state,
   or acceptance evidence is unavailable, say so and preserve the uncertainty.
10. **Do not reconcile terminal items by default.** Issues in a terminal Jira
    status or Done-category status are excluded from the active ranked review
    queue. Keep them as read-only historical or relationship context. Review a
    terminal item only when the user names it explicitly or an active item's
    relationship requires it for a specific decision; do not reopen or modify it
    implicitly.

## Workflow phases

Work through these phases in order. Each phase's detailed instructions live in
a bundled reference file; read it when entering the phase.

| Phase | Purpose | Reference |
|---|---|---|
| 0. Configure the reconciliation run | Establish boundaries, capabilities, and write mode | [references/run-configuration.md](references/run-configuration.md) |
| 1. Collect the Jira project | Build a complete snapshot and the ranked active queue | [references/jira-snapshot.md](references/jira-snapshot.md) |
| 2. Collect repository history | Inventory repositories and verify mainline evidence | [references/repository-evidence.md](references/repository-evidence.md) |
| 3. Evaluate Jira against evidence | Classify each active issue with evidence assessments | [references/evaluation.md](references/evaluation.md) |
| 4. Reconcile ranked items interactively | Present one issue at a time with exact proposals | [references/item-review.md](references/item-review.md) |
| Staging file format | Record the durable, reviewable audit and proposals | [references/staging.md](references/staging.md) |
| Per-item approval and verification | Approve, preflight, apply, and verify one item at a time | [references/approval-loop.md](references/approval-loop.md) |

Phase summary:

- **Phase 0** records the Jira scope or handoff baseline, repository inventory,
  mainline refs, GitHub access, staging destination, activity window, and write
  mode, plus a Jira and GitHub capability table.
- **Phase 1** builds a complete, paginated Jira snapshot, sorts non-terminal
  issues by Rank, and retains terminal issues in a read-only reference set.
- **Phase 2** inventories repositories, runs history searches, and verifies the
  mainline boundary for every claim that matters. A branch, open pull request,
  or working-tree file is not merged delivery.
- **Phase 3** compares Jira state, repository history, and operational state,
  and assigns evidence assessments such as `Not started`, `In Progress`,
  `Branch-only`, or `Complete`. Evidence assessments are not automatic status
  transitions.
- **Phase 4** walks the ranked non-terminal queue, presents evidence and exact
  old-value/new-value proposals for each issue, and stages the decision.
- The staging file is the durable phase boundary and operation ledger for every
  proposed Jira or GitHub write.
- The approval loop approves, preflights, applies, and verifies exactly one
  item at a time, with no implicit full-batch approval.

## Resume and stop conditions

The staging file is the phase boundary. A later session must be able to resume
from it without depending on the chat transcript. Before stopping, record:

- Jira snapshot source and freshness
- Repository baseline commits
- Issues reviewed and their decisions
- The next Rank to review
- Open design, ownership, target-environment, and access questions
- The current item's approval, preflight, submission, and verification state
- Whether any Jira or GitHub changes were submitted

Stop and ask the user when:

- The Jira project or Rank source is ambiguous
- The relevant mainline cannot be identified
- A proposed status or architecture change depends on an unresolved decision
- The user has not approved moving to the next issue
- Jira or GitHub writes are requested before the current item is approved
