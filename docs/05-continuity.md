# 5. Continuity: Does the Validated State Still Hold at the Consequence Boundary?

Binding establishes exactly what was authorized.

It does **not** establish that the conditions supporting that authorization will remain valid until execution.

Between the creation of a bounded authorization at time `t0` and an execution attempt at time `t1`, the enterprise state may change.

Authority may be revoked.

Evidence may become stale.

A policy may change.

A human approval may expire.

The protected resource may change state.

The authorized executor may no longer be the same trusted principal.

An incident may increase the risk associated with the action.

The execution authorization itself may expire or already have been consumed.

The architecture must therefore distinguish:

```text
Valid at binding time
        ≠
Valid at execution time
```

Continuity is the mechanism that closes this temporal gap.

It asks:

> **Do the governance conditions that justified this authorization still hold when the system is ready to create consequence?**

---

## 5.1 The Time-of-Check / Time-of-Use Problem

Suppose a transfer is evaluated at:

```text
t0 = 09:00:00
```

At that moment:

```text
Authority      = CURRENT
Evidence       = SUFFICIENT
Policy         = SATISFIED
Approval       = PRESENT
Risk           = HIGH
Executor       = Payments-Service-Prod
```

The action receives:

```text
ALLOW
```

and is successfully bound.

Execution does not occur immediately.

At:

```text
t1 = 09:02:30
```

one of the following happens:

```text
Authority revoked
```

or:

```text
Approval withdrawn
```

or:

```text
Beneficiary status changed
```

or:

```text
Policy version updated
```

or:

```text
Executor identity changed
```

If the system executes solely because the authorization was valid at `t0`, it has converted historical validity into present permission.

That is unsafe.

The correct question at the consequence boundary is:

```text
Is the authorization still valid now?
```

---

## 5.2 Bound State and Current State

Let the governance state relied upon during binding be:

```text
G_t0
```

The current governance state at execution time is:

```text
G_t1
```

Continuity therefore evaluates the relationship between:

```text
G_t0
```

and:

```text
G_t1
```

A simple equality test is insufficient.

Some fields may be allowed to change.

Other fields may require re-evaluation.

Some changes may invalidate the authorization immediately.

Continuity is therefore better represented as:

```text
K_t1 =
    Continuity(
        Authorization,
        G_t0,
        G_t1,
        ExecutionContext_t1
    )
```

where:

```text
K_t1 ∈ {
    VALID,
    REVALIDATE,
    HOLD,
    ESCALATE,
    REJECT
}
```

---

## 5.3 Continuity Is Not Repeating the Entire Workflow Blindly

Continuity does not necessarily require rerunning every governance operation from the beginning for every execution attempt.

Instead, it determines which governance dimensions must still be proven current.

For example:

```text
Bound Authorization
        ↓
Continuity Check
        ├── Authority current?
        ├── Identity / standing current?
        ├── Evidence still valid?
        ├── Scope unchanged?
        ├── Policy still applicable?
        ├── Risk materially changed?
        ├── Approval still valid?
        ├── Resource state compatible?
        ├── Executor still valid?
        ├── Authorization unexpired?
        └── Authorization unconsumed?
```

The required checks should depend on the consequence class and enterprise policy.

---

## 5.4 The Continuity Vector

The continuity state can be modeled as a vector:

```text
K_t = {
    I_t,
    U_t,
    E_t,
    S_t,
    P_t,
    R_t,
    H_t,
    X_t,
    Q_t,
    T_t,
    N_t
}
```

where:

| Symbol | Continuity Dimension            |
| ------ | ------------------------------- |
| `I_t`  | Identity / standing             |
| `U_t`  | Authority                       |
| `E_t`  | Evidence                        |
| `S_t`  | Scope / delegation              |
| `P_t`  | Policy                          |
| `R_t`  | Risk                            |
| `H_t`  | Human approval                  |
| `X_t`  | Executor state                  |
| `Q_t`  | Protected resource state        |
| `T_t`  | Authorization temporal validity |
| `N_t`  | Replay / consumption state      |

The exact dimensions may vary by implementation.

The architectural requirement is that material state capable of changing execution legitimacy must not be silently ignored.

---

## 5.5 Identity and Standing Continuity

The system must establish that the relevant principal still has valid standing.

At binding:

```text
IdentitySnapshot = ID-772
Standing         = CURRENT
```

At execution:

```text
Standing_current
```

must still satisfy the required condition.

A change such as:

```text
CURRENT → REVOKED
```

must invalidate continuation.

Similarly:

```text
CURRENT → UNCERTAIN
```

should not silently inherit the prior result.

A possible outcome is:

```text
UNCERTAIN
    → HOLD
```

or:

```text
UNCERTAIN
    → ESCALATE
```

depending on policy.

---

## 5.6 Authority Continuity

Authority is one of the most important continuity dimensions.

At binding:

```text
authority_snapshot_id = AUTH-9841
```

The authorization proves that this authority state supported the decision at `t0`.

At execution, the system must determine whether the relevant authority remains current.

Conceptually:

```text
AuthorityContinuity =
    CheckCurrentAuthority(
        actor,
        action,
        resource,
        bound_authority_snapshot
    )
```

Possible outcomes:

```text
CURRENT
WEAKENED
UNCERTAIN
ABSENT
REVOKED
```

For a consequence-bearing action:

```text
REVOKED
    → REJECT
```

while:

```text
UNCERTAIN
    → HOLD / ESCALATE
```

may be appropriate.

The exact outcome is policy-specific.

The architectural requirement is not.

> **A previously valid authority state must not automatically survive a material authority change.**

---

## 5.7 Evidence Continuity

Evidence used at admissibility may no longer be valid at execution.

For example:

```text
Invoice status at t0:
    APPROVED

Invoice status at t1:
    CANCELLED
```

or:

```text
Beneficiary status at t0:
    VERIFIED

Beneficiary status at t1:
    UNDER_REVIEW
```

The original evidence remains historically relevant.

But it may no longer support execution.

Therefore:

```text
EvidenceContinuity =
    Evaluate(
        BoundEvidence,
        CurrentEvidenceState
    )
```

Possible states may include:

```text
CURRENT
STALE
SUPERSEDED
CONFLICTED
INVALID
```

A materially changed evidence state should invalidate inherited execution permission.

---

## 5.8 Scope and Delegation Continuity

Delegation can change after binding.

Suppose:

```text
t0:
Treasury-Agent-01 may initiate payments up to ₹500,000
```

The transfer is bound.

Before execution:

```text
t1:
Limit reduced to ₹100,000
```

The proposed transfer remains:

```text
₹250,000
```

The original binding is historically valid.

The current delegation is not.

Continuity must therefore re-establish:

```text
CurrentScope(a_execution)
    includes
BoundAction(a)
```

Otherwise:

```text
OUT_OF_SCOPE
    → REJECT
```

or, where policy allows reconsideration:

```text
→ ESCALATE
```

---

## 5.9 Policy Continuity

Binding records the policy basis used during admissibility.

For example:

```text
policy_id      = TREASURY-PAYMENTS
policy_version = 17
```

At execution time the active policy may be:

```text
policy_version = 18
```

A policy-version change does not automatically mean the authorization is invalid.

But it must trigger an explicit policy decision.

Possible strategies include:

```text
Grandfather bound authorization
```

or:

```text
Require re-evaluation under current policy
```

or:

```text
Invalidate authorization immediately
```

The appropriate strategy is enterprise-specific.

The architecture requires that the transition be explicit.

It must not become:

```text
Policy changed
    ↓
ignore change
    ↓
execute anyway
```

---

## 5.10 Human Approval Continuity

Approval is also stateful.

At binding:

```text
approval_id = APR-118
status      = PRESENT
```

At execution:

```text
approval_status
```

may have changed to:

```text
REVOKED
EXPIRED
SUPERSEDED
```

Continuity must verify that:

```text
Approval_current
```

still applies to:

```text
BoundActionHash
```

and remains within its permitted lifetime and scope.

A revoked approval must not continue to authorize execution merely because the authorization object still contains the historical approval reference.

---

## 5.11 Risk Continuity

Risk can change after binding even if the action itself does not.

Examples include:

- fraud alert raised;
- cybersecurity incident declared;
- beneficiary risk classification increased;
- market volatility threshold crossed;
- system health degraded;
- transaction velocity increased;
- related account placed under investigation.

Therefore:

```text
Risk_t0
    ≠>
Risk_t1
```

If a material risk change occurs, continuity may require:

```text
REVALIDATE
```

or:

```text
ESCALATE
```

or:

```text
REJECT
```

depending on policy.

---

## 5.12 Executor Continuity

Section 4 bound the authorization to an executor.

For example:

```text
executor_id = Payments-Service-Prod
```

Continuity must verify not only that the identifier matches, but that the current executor remains acceptable.

Depending on implementation, this may include:

- workload identity;
- certificate validity;
- service principal status;
- runtime attestation;
- environment;
- deployment version;
- security posture;
- tenant context.

Conceptually:

```text
Executor_current
    =
Executor_bound
```

is necessary.

But for high-consequence systems it may not be sufficient.

The executor may also need to satisfy current trust conditions.

---

## 5.13 Protected Resource Continuity

The target resource can change between authorization and execution.

For example:

```text
Bound Resource:
Corporate-Account-01
```

may later become:

```text
Frozen
```

or:

```text
Closed
```

or:

```text
Restricted
```

or:

```text
Ownership changed
```

Therefore continuity should evaluate whether the current resource state remains compatible with the authorized action.

Conceptually:

```text
ResourceCompatible(
    BoundAction,
    ResourceState_current
)
```

must hold.

---

## 5.14 Temporal Continuity

Section 4 defined:

```text
t_issue <= t_current < t_expiry
```

Continuity must enforce this condition at the moment the authorization is presented for execution.

If:

```text
t_current >= t_expiry
```

then:

```text
REJECT_EXPIRED
```

There should be no automatic grace period unless explicitly defined by policy.

An expired authorization may be re-evaluated and rebound.

It should not simply be revived.

---

## 5.15 Replay-State Continuity

The authorization must also remain unused where single-use semantics apply.

At execution:

```text
Consumed(AuthZ_id)?
```

must be checked.

If:

```text
TRUE
```

then:

```text
REPLAY_REJECTED
```

This must be coordinated with the actual commit operation.

Otherwise two concurrent requests could both pass the continuity check before either marks the authorization consumed.

This introduces an important architectural requirement:

> **Some continuity conditions must be validated atomically with execution.**

---

## 5.16 Material Change

Not every state difference requires invalidation.

The system therefore needs an explicit definition of material change.

Conceptually:

```text
MaterialChange =
    f(
        action,
        authority,
        evidence,
        scope,
        policy,
        risk,
        approval,
        executor,
        resource,
        time,
        replay_state
    )
```

Examples of material changes may include:

```text
amount changed
beneficiary changed
authority revoked
approval expired
policy materially changed
resource frozen
risk escalated
executor identity changed
authorization expired
authorization consumed
```

Examples of non-material changes might include:

```text
display label changed
non-security metadata updated
logging correlation ID changed
presentation formatting changed
```

The materiality rules must be explicit and testable.

---

## 5.17 Continuity Outcomes

A useful continuity result model is:

```text
VALID
REVALIDATE
HOLD
ESCALATE
REJECT
```

### VALID

All required continuity conditions still hold.

The authorization may proceed toward enforcement and commit.

### REVALIDATE

A material state change requires a fresh governance evaluation.

The previous authorization must not be used directly.

### HOLD

Execution is temporarily paused because required current state is unavailable or unresolved.

### ESCALATE

Current conditions require higher authority or human intervention.

### REJECT

The authorization is no longer valid for execution.

Examples include:

- revoked authority;
- expired authorization;
- consumed authorization;
- invalid executor;
- prohibited resource state;
- revoked approval.

---

## 5.18 Continuity State Machine

A conceptual state machine is:

```text
BOUND
  ↓
CONTINUITY_CHECK
  ├──→ VALID
  ├──→ REVALIDATE
  ├──→ HOLD
  ├──→ ESCALATE
  └──→ REJECT
```

Only:

```text
VALID
```

may proceed toward the commit boundary.

A `REVALIDATE` outcome returns the action to governance evaluation.

Conceptually:

```text
BOUND
  ↓
CONTINUITY_CHECK
  ↓
REVALIDATE
  ↓
ADMISSIBILITY
  ↓
BINDING
```

A new authorization should be created if the action is subsequently allowed.

The previous authorization should not be silently updated in place.

---

## 5.19 Commit-Time Revalidation

A continuity check performed several seconds before execution may itself become stale.

For high-consequence actions, the strongest validation point is therefore immediately before protected mutation.

Conceptually:

```text
Authorization Presented
        ↓
Continuity Validation
        ↓
Commit-Time Revalidation
        ↓
Protected Mutation
```

The interval between the final check and the state mutation should be minimized.

For checks whose state can change independently, implementations may require:

- transactional validation;
- atomic compare-and-set;
- database constraints;
- version checks;
- locks;
- conditional writes;
- short-lived signed proofs;
- policy-engine decisions bound to transaction state.

The architecture does not prescribe a universal mechanism.

It requires that the final consequence not depend on a materially stale validation result.

---

## 5.20 The Commit Boundary

The **commit boundary** is the final point before irreversible or externally meaningful state mutation.

Conceptually:

```text
Reasoning
    ↓
Admissibility
    ↓
Binding
    ↓
Continuity
    ↓
-------------------------
      COMMIT BOUNDARY
-------------------------
    ↓
Execution
    ↓
Consequence
```

At this boundary, the system must establish at least:

```text
Exact action matches
Executor matches
Authorization unexpired
Authorization unconsumed
Authority current
Approval current
Required evidence valid
Applicable policy satisfied
Resource state compatible
Required risk conditions satisfied
```

Only then may the system proceed to mutation.

---

## 5.21 Continuity and the Bank Transfer Example

Consider:

```text
Action:
Transfer ₹250,000
from Corporate-Account-01
to Vendor-ABC
```

At `t0`:

```text
Capability      = VALID
Identity        = CURRENT
Authority       = CURRENT
Evidence        = SUFFICIENT
Scope           = IN_SCOPE
Policy          = SATISFIED
Risk            = HIGH
Approval        = PRESENT
```

The action receives:

```text
ALLOW
```

and a bounded authorization is created.

At `t1`, immediately before commit:

```text
Capability      = VALID
Identity        = CURRENT
Authority       = CURRENT
Evidence        = SUFFICIENT
Scope           = IN_SCOPE
Policy          = SATISFIED
Risk            = HIGH
Approval        = PRESENT
Executor        = MATCH
Resource        = ACTIVE
Authorization  = UNEXPIRED
Replay State    = UNUSED
```

Continuity result:

```text
VALID
```

The action may proceed toward enforcement.

Now change one condition:

```text
Approval = REVOKED
```

Continuity becomes:

```text
REJECT
```

The original `ALLOW` decision and valid binding do not override the current approval state.

This is exactly why continuity exists.

---

## 5.22 Invariant C7 — Governance Continuity

For any bounded authorization:

```text
MaterialGovernanceChange(
    G_t0,
    G_t1
)
    =>
NoInheritedExecutionPermission
```

Meaning:

> **A material change in governance state after binding must prevent automatic inheritance of execution permission.**

The action must be revalidated, held, escalated, or rejected according to policy.

---

## 5.23 Invariant C8 — Commit-Time Revalidation

For a protected execution:

```text
ProtectedMutation(a)
    =>
ContinuityValid(a, t_commit)
```

Meaning:

> **A protected state mutation may occur only if required continuity conditions are valid at the commit boundary.**

A validation result from an earlier point in time is insufficient if material state could have changed before commit.

---

## 5.24 What Continuity Proves — and What It Does Not

Continuity can establish that:

- the relevant identity and standing remain valid;
- authority remains current;
- evidence remains usable;
- scope remains compatible;
- policy remains applicable;
- risk conditions remain acceptable;
- approval remains valid;
- the executor remains acceptable;
- the protected resource remains compatible;
- the authorization has not expired;
- the authorization has not already been consumed;
- no material change requires re-evaluation.

Continuity does **not** by itself prove that:

- the executor is structurally forced to honor the result;
- the protected system will refuse unauthorized mutation;
- another execution route cannot bypass governance;
- the final state mutation occurred exactly as intended.

Those are enforcement and route-closure obligations.

---

## 5.25 Continuity Architecture

![Figure 5 — Continuity: Revalidate Before Consequence](../diagrams/figure-05-continuity.png)

**Figure 5 — Continuity: Does the Validated State Still Hold at the Consequence Boundary?**

---

## 5.26 Section 5 Conclusion

Binding answers:

> **What exactly was authorized?**

Continuity answers:

> **Is that authorization still legitimate now?**

The architecture has now established:

```text
Proposed Action
      ↓
Admissibility
      ↓
Binding
      ↓
Continuity
```

But one major problem remains.

A governance system can produce a correct continuity result and still fail if the executor is free to ignore it.

The next question is therefore:

> **How is a valid governance decision made structurally unavoidable for consequence-bearing execution?**

That is the enforcement problem.


---

## Continue

**Next Section:** [Section 6 — Enforcement](06-enforcement.md)

**Return to Main:** [Architecture Index](README.md)

