# 4. Binding: How an Admissibility Decision Becomes Controlling

Section 3 established:

```text
ALLOW ≠ EXECUTE
```

and:

```text
ALLOW = ELIGIBLE_FOR_BINDING
```

Binding converts an admissibility determination into a bounded authorization for one specific action, resource, executor, and validated governance state.

## 4.1 The Binding Problem

This is insufficient:

```text
decision = ALLOW
actor    = Treasury-Agent-01
action   = transfer_funds
```

It does not specify source account, beneficiary, amount, resource, tenant, environment, executor, authority state, evidence state, policy version, approval, authorization lifetime, replay behavior, or integrity proof.

A stronger statement is:

> **Payments-Service-Prod may execute this exact ₹250,000 transfer from Corporate-Account-01 to Vendor-ABC, based on the validated governance state identified in this authorization, before its expiry, and subject to continuity validation.**

## 4.2 Binding Function

```text
D_t0 = Γ(a_t0, G_t0)
```

where:

```text
D_t0 = ALLOW
```

Then:

```text
B_t0 = Bind(
    D_t0,
    a_t0,
    G_t0,
    X
)
```

The output is a bounded execution authorization candidate.

```text
Admissibility Decision
        +
Exact Action
        +
Resource
        +
Executor
        +
Governance State References
        +
Validity Window
        +
Replay Protection
        +
Integrity Proof
        ↓
Bound Execution Authorization
```

## 4.3 Action Binding

```text
Canonical(a)
```

Then:

```text
ActionHash = H(Canonical(a))
```

At execution:

```text
H(Canonical(a_execution))
    =
ActionHash_bound
```

If:

```text
H(a_execution) ≠ H(a_bound)
```

the authorization no longer applies.

## 4.4 Canonicalization Is Part of the Security Boundary

Canonicalization should define at minimum:

- deterministic field ordering;
- numeric normalization;
- currency representation;
- Unicode normalization;
- date/time representation;
- explicit optional-field semantics;
- explicit null semantics;
- distinction between material and non-material metadata.

## 4.5 Resource Binding

```text
ResourceBinding = {
    resource_type,
    resource_id,
    tenant,
    environment
}
```

Example:

```text
resource_type = bank_account
resource_id   = Corporate-Account-01
tenant        = Enterprise-A
environment   = production
```

At execution:

```text
AuthorizedResource(a)
    =
BoundResource
```

Otherwise:

```text
RESOURCE_MISMATCH
    → REJECT
```

## 4.6 Executor Binding

Requesting actor and authorized executor are distinct:

```text
Requesting Actor:
    Treasury-Agent-01

Authorized Executor:
    Payments-Service-Prod
```

At execution:

```text
Executor_execution
    =
X_bound
```

Otherwise:

```text
EXECUTOR_MISMATCH
    → REJECT
```

Executor binding does **not** prove that no alternate path exists. That is the route-closure problem.

## 4.7 Governance-State Binding

Bind stable references or immutable snapshots such as:

```text
capability_snapshot_id
identity_snapshot_id
authority_snapshot_id
evidence_snapshot_id
scope_snapshot_id
policy_version
risk_classification_id
approval_id
```

These references support both continuity validation and later provenance.

## 4.8 Authority-State Binding

```text
BoundAuthoritySnapshot
    ≠
CurrentAuthorityProof
```

The bound snapshot proves what authority state was relied upon when the authorization was created. It does not prove that authority still holds later.

## 4.9 Evidence-State Binding

```text
EvidenceHash =
    H(
        Canonical(EvidenceManifest)
    )
```

But:

```text
EvidenceWasValid_t0
    ≠>
EvidenceIsValid_t1
```

## 4.10 Policy Binding

```text
PolicyBinding = {
    policy_id: "TREASURY-PAYMENTS",
    policy_version: 17
}
```

The policy basis must remain explicit enough for continuity and provenance.

## 4.11 Human Approval Binding

Do not reduce approval to:

```text
approved = true
```

Instead preserve:

```text
approval_id
approver_identity
approved_action_hash
approval_scope
approval_timestamp
approval_expiry
```

And require:

```text
ApprovedActionHash
    =
BoundActionHash
```

## 4.12 Binding Time and Expiration

```text
t_issue
t_expiry
```

Valid only when:

```text
t_issue <= t_current < t_expiry
```

Otherwise:

```text
EXPIRED
    → REJECT
```

A conceptual TTL:

```text
TTL =
    f(
        consequence,
        authority_volatility,
        evidence_volatility,
        risk,
        policy
    )
```

## 4.13 Replay Protection

Use a unique authorization identifier or nonce.

Where single-use semantics apply:

```text
Consume(AuthZ_id)
```

must be atomic.

```text
First Use
    → VALID

Second Use
    → REPLAY_REJECTED
```

## 4.14 Integrity Protection

Conceptually:

```text
Signature =
    Sign(
        K_private,
        H(
            Canonical(ExecutionAuthorization)
        )
    )
```

Equivalent mechanisms may include asymmetric signatures, signed tokens, keyed MACs, hardware-backed signatures, or workload-attested authorization objects.

> **Material authorization fields must not be modifiable without invalidating the authorization.**

## 4.15 Conceptual Execution Authorization

```json
{
  "authorization_id": "AUTHZ-7F92",
  "decision_id": "DEC-1882",
  "actor_id": "Treasury-Agent-01",
  "executor_id": "Payments-Service-Prod",
  "action_type": "transfer_funds",
  "action_hash": "sha256:...",
  "resource": {
    "type": "bank_account",
    "id": "Corporate-Account-01",
    "tenant": "Enterprise-A",
    "environment": "production"
  },
  "governance_state": {
    "capability_snapshot_id": "CAP-491",
    "identity_snapshot_id": "ID-772",
    "authority_snapshot_id": "AUTH-9841",
    "evidence_snapshot_id": "EVD-225",
    "scope_snapshot_id": "SCP-773",
    "policy": {
      "id": "TREASURY-PAYMENTS",
      "version": 17
    },
    "risk_classification": "HIGH",
    "approval_id": "APR-118"
  },
  "issued_at": "2026-08-19T09:00:00Z",
  "expires_at": "2026-08-19T09:05:00Z",
  "nonce": "9ecf...",
  "signature": "..."
}
```

This schema is illustrative rather than normative.

## 4.16 Binding Does Not Create Permanent Permission

Between binding and execution, governance-relevant conditions may change.

Therefore:

```text
Bound_t0
    ≠>
NecessarilyExecutable_t1
```

Binding proves what was authorized. It does not prove that the authorization remains executable later.

## 4.17 Binding State Machine

```text
ALLOW
  ↓
BINDING
  ├──→ ACTION_MISMATCH
  ├──→ RESOURCE_MISMATCH
  ├──→ EXECUTOR_UNRESOLVED
  ├──→ SNAPSHOT_FAILURE
  ├──→ SIGNING_FAILURE
  └──→ BOUND
```

Only `BOUND` may proceed to continuity.

## 4.18 Binding Invariants

### Invariant C3 — Exact Action Binding

```text
ExecutionActionHash
    =
BoundActionHash
```

Otherwise:

```text
REJECT
```

### Invariant C4 — Executor Binding

```text
Executor_current
    =
Executor_bound
```

Otherwise:

```text
REJECT
```

### Invariant C5 — Bounded Authorization Lifetime

```text
t_issue <= t_current < t_expiry
```

Otherwise:

```text
REJECT_EXPIRED
```

### Invariant C6 — Single-Use Where Required

```text
Consumed(AuthZ_id)
    =>
NOT Reusable(AuthZ_id)
```

Enforcement must be atomic.

## 4.19 What Binding Proves — and What It Does Not

Binding can establish that a governance decision applies to:

- a specific decision;
- a specific actor;
- a specific action;
- a specific protected resource;
- a specific executor;
- specific governance-state references;
- a specific policy version;
- a specific approval;
- a bounded validity period;
- a unique authorization object;
- an integrity-protected representation.

Binding does **not** by itself prove:

- that authority is still current;
- that evidence is still valid;
- that approval has not been revoked;
- that policy remains applicable;
- that the resource state has not materially changed;
- that the executor remains trustworthy;
- that an alternate execution path does not exist.

## 4.20 Binding Architecture

<!-- IMAGE PLACEHOLDER: FIGURE 4 -->

![Figure 4 — Binding: From ALLOW to Bounded Execution Authorization](../diagrams/figure-04-binding.png)

**Figure 4 — Binding: From ALLOW to Bounded Execution Authorization.**

## 4.21 Section 4 Conclusion

Admissibility asks:

> **Should this action be permitted under the current governance state?**

Binding asks:

> **Exactly which action, resource, executor, and validated state does that decision apply to?**

The system must still answer:

> **Do the conditions that justified this authorization still hold when the system is ready to create consequence?**



---