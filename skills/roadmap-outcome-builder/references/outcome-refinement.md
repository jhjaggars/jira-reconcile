# Phases 1-2: Capture and refine outcomes

## Phase 1: Capture the informal list

Accept fragments, shorthand, internal terminology, and incomplete ideas without
prematurely turning them into tickets. Normalize the list only enough to identify
candidate outcomes.

For each candidate, write:

```text
Working outcome: [what users or operators will be able to do]
Likely value: [why it matters]
Unknowns: [the smallest questions that change the scope]
```

Group duplicate or overlapping bullets, but do not merge distinct outcomes just
because they share infrastructure. External secret storage, user access,
confidential workloads, and gateway upgrades may share implementation machinery
while remaining separate roadmap outcomes.

## Phase 2: Refine one outcome at a time

Use this structure for each candidate.

### Outcome title

Use a concise product statement:

- `Enable existing SSO users to access managed gateways`
- `Offer confidential-container hosting for managed workloads`
- `Provide managed lifecycle upgrades for gateways`

Avoid implementation-only titles such as `Add a secret-manager adapter` or
`Implement a runtime class` unless those are genuinely the user-visible outcome.

### Press-release statement

Write one paragraph answering:

- Who benefits?
- What can they do now?
- What problem or adoption blocker is removed?
- What is intentionally not promised?

### User workflow

Describe the visible sequence from the user's perspective. Keep implementation
behind the service boundary when users do not choose it. If the service configures
an external provider, do not present the provider choice as a user feature unless
users actually select it.

### Included scope

List the capabilities required for the outcome to be real. Include operational
behavior when it materially affects the promise:

- security and authorization;
- lifecycle and recovery;
- observability;
- supported environments;
- migration or non-migration behavior;
- documentation and operator workflow.

### Explicit exclusions

State what the outcome does not include. Typical exclusions include:

- existing resources are not migrated;
- users do not choose an implementation strategy;
- automatic RBAC is not included;
- a control plane is not duplicated;
- zero downtime is not promised yet;
- a technology is supported only where the upstream project supports it.

### Completion signal

Write a testable statement of what must be true before the outcome can be
announced. This is a product acceptance target, not evidence that the target has
already been met. Reconciliation later determines whether the target is met.

### Refinement questions

Ask only the questions that change the outcome:

- Which exact object or user is in scope?
- Is the capability for new resources, existing resources, or both?
- Who operates the feature and who selects it?
- What happens when a prerequisite is unavailable?
- Is a manual step acceptable, or must the service automate it?
- What is the minimum initial release versus the ideal future state?
- Which availability or compatibility promises are still unknown?

Do not move to the next outcome until the user confirms that the current one is
accurate. Record the locked wording in the staging artifact or a roadmap draft.
