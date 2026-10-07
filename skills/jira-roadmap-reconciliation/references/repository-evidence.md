# Phase 2: Collect repository history

Create a repository inventory before searching for implementation evidence.
Include:

- `$PROJECT`
- Every repository in `RELATED_PROJECTS`
- Repositories named in project documentation, remotes, submodules, manifests,
  or deployment instructions when they are relevant to the reviewed issues

For every repository, record:

- Absolute path
- Current working-tree status
- The explicit agreed mainline ref and commit
- HEAD and relevant remote refs
- Whether uncommitted changes exist
- The date and scope of the history audit

Use path-specific and topic-specific history searches, then inspect the actual
files and specifications behind promising commits. Prefer evidence such as:

- `git log --all -- <path>`
- `git log --all --grep=<term>`
- `git show --stat --summary <commit>`
- `git show <commit>:<path>`
- Current files reachable from the mainline
- Tests, generated manifests, deployment overlays, runbooks, and qualification
  results

For every implementation claim that matters to a Jira proposal, verify the
mainline boundary explicitly, for example:

```shell
git -C /absolute/repository merge-base --is-ancestor <commit> <mainline-ref>
```

Do not treat an unmerged branch, an open pull request, a remote branch name, or
an uncommitted working-tree file as merged delivery. Record branch-only evidence
because it may explain Jira activity, but label it clearly.

## Evidence ledger

Use a normalized ledger with at least these columns:

| Evidence | Repository | Ref or commit | Mainline? | Evidence class | What it proves | What it does not prove |
|---|---|---|---|---|---|---|

The final column is important. A manifest may prove intended configuration but
not a live deployment. A unit test may prove behavior in isolation but not
provider acceptance. A specification may define desired state but not delivery.
