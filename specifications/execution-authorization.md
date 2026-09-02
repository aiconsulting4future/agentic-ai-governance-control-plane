# Execution Authorization Contract

## The Agentic AI Governance Control Plane

**Version:** v0.1  
**Status:** Complete Specification  
**Specification Type:** Bounded Execution Authorization Contract

---

## 1. Purpose

This specification defines the canonical structure and control semantics of a **Bounded Execution Authorization** within **The Agentic AI Governance Control Plane**.

The authorization contract answers:

> **What exact action, on what resource, by which executor, under which validated governance state, may proceed toward execution — and for how long?**

The authorization exists because:

```text
ALLOW ≠ EXECUTE
```

A runtime admissibility decision of `ALLOW` means only:

```text
ALLOW
    ↓
ELIGIBLE_FOR_BINDING
```

Binding converts that decision into a bounded authorization for one exact action, resource, executor, governance-state basis, validity window, replay policy, and integrity-protected authorization object.

Conceptually:

```text
Valid Action Contract
        ↓
Runtime Admissibility
        ↓
ALLOW
        ↓
Binding
        ↓
EXECUTION AUTHORIZATION
        ↓
Continuity
        ↓
Enforcement
```

The execution authorization is therefore **not**:

- a model recommendation;
- a generic role grant;
- a standing permission;
- a tool capability;
- an API credential;
- a permanent entitlement;
- or proof that execution is still legitimate at commit time.

It is a **bounded, integrity-protected authorization candidate** that must still survive continuity and enforcement.

---

## 2. Architectural Role

The execution authorization is the primary artifact produced by the **Binding** stage.

Binding establishes:

```text
what action
+
which resource
+
which executor
+
which governance state
+
which policy
+
which approval
+
which validity window
+
which replay semantics
+
which integrity proof
```

apply to the authorization.

The architecture maintains the distinction:

```text
Decision Exists
    ≠
Decision Controls Execution
```

and:

```text
Bound Authorization
    ≠
Currently Executable
```

A bounded authorization proves what was authorized at binding time.

It does not prove that the authorization remains legitimate later.

---

# 3. Preconditions for Authorization Issuance

An execution authorization **MUST NOT** be issued merely because an action contract is structurally valid.

The minimum precondition is:

```text
ActionContract = VALID
AND
AdmissibilityDecision = ALLOW
```

Conceptually:

```text
D_t0 = Γ(a_t0, G_t0)

where:

D_t0 = ALLOW
```

Only then may the system perform:

```text
B_t0 = Bind(
    D_t0,
    a_t0,
    G_t0,
    X
)
```

where `X` is the intended executor.

### Requirement

The binding service **MUST** verify that the `ALLOW` decision applies to the same action identity and material action state being bound.

A `HOLD`, `ESCALATE`, or `DENY` decision **MUST NOT** produce an execution authorization for the same material action state.

---

# 4. Canonical Authorization Object

A conceptual authorization object is:

```json
{
  "schema_version": "0.1",
  "authorization_id": "AUTHZ-7F92",
  "decision_id": "DEC-1882",
  "action_id": "ACT-2041",
  "actor_id": "Treasury-Agent-01",
  "executor_id": "Payments-Service-Prod",
  "action_type": "transfer_funds",
  "action_hash": "sha256:...",
  "resource": {
    "resource_type": "bank_account",
    "resource_id": "Corporate-Account-01",
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
      "policy_id": "TREASURY-PAYMENTS",
      "policy_version": 17
    },
    "risk_classification_id": "RISK-442",
    "risk_classification": "HIGH",
    "approval_id": "APR-118"
  },
  "issued_at": "2026-08-24T08:00:00Z",
  "expires_at": "2026-08-24T08:05:00Z",
  "usage": {
    "mode": "single_use",
    "nonce": "9ecf..."
  },
  "integrity": {
    "algorithm": "implementation-defined",
    "signature": "..."
  }
}
```

The schema above is normative in **control semantics**, but implementations may choose a different serialization or cryptographic envelope.

---

# 5. Required Top-Level Fields

## 5.1 `schema_version`

Example:

```text
0.1
```

### Requirement

The authorization **MUST** identify the schema semantics under which it was created.

A material schema interpretation change **MUST NOT** occur silently.

---

## 5.2 `authorization_id`

Example:

```text
AUTHZ-7F92
```

A unique identifier for the bounded authorization.

### Requirement

The identifier **MUST** remain stable across:

```text
binding
continuity
enforcement
execution
provenance
```

The same authorization identifier **MUST NOT** refer to two materially different authorization payloads.

---

## 5.3 `decision_id`

Example:

```text
DEC-1882
```

The identifier of the admissibility decision that permitted binding.

### Requirement

The authorization **MUST** be traceably linked to the decision that created eligibility for binding.

This supports:

```text
Decision
    ↓
Authorization
```

and later:

```text
Decision
    ↓
Authorization
    ↓
Execution
```

---

## 5.4 `action_id`

Example:

```text
ACT-2041
```

The identifier of the proposed action represented by the Action Contract.

### Requirement

The authorization **MUST** refer to one specific governed action instance or immutable action version.

---

## 5.5 `actor_id`

Example:

```text
Treasury-Agent-01
```

The actor that originated the governed action.

### Requirement

`actor_id` establishes action provenance.

It does not by itself prove current authority.

Current authority is a continuity concern.

---

## 5.6 `executor_id`

Example:

```text
Payments-Service-Prod
```

The principal authorized to carry the protected execution.

### Requirement

The authorization **MUST** identify the authorized executor when executor identity is material to the protected consequence.

At enforcement:

```text
Executor_current
    =
Executor_bound
```

must hold.

Otherwise:

```text
REJECT
```

---

## 5.7 `action_type`

Example:

```text
transfer_funds
```

### Requirement

The authorization **MUST** preserve the semantic action type that was governed and bound.

---

## 5.8 `action_hash`

Conceptually:

```text
ActionHash =
    H(Canonical(ActionContract))
```

### Requirement

The authorization **MUST** bind to the canonical material representation of the governed action.

At enforcement:

```text
ExecutionActionHash
    =
BoundActionHash
```

must hold.

This is the core mechanism supporting **C3 — Exact Action Binding**.

---

# 6. Resource Binding

The authorization binds the action to a protected resource.

Conceptually:

```json
{
  "resource_type": "bank_account",
  "resource_id": "Corporate-Account-01",
  "tenant": "Enterprise-A",
  "environment": "production"
}
```

### Requirement

For resource-specific consequence:

```text
ExecutionResource
    =
BoundResource
```

must hold.

An authorization for:

```text
Corporate-Account-01
```

must not authorize mutation of:

```text
Corporate-Account-02
```

unless a new governance decision and binding explicitly permit it.

### Tenant and environment binding

The resource context **SHOULD** preserve tenant and environment boundaries.

For example:

```text
Enterprise-A / production
```

must not silently become:

```text
Enterprise-B / production
```

or:

```text
Enterprise-A / staging
```

if those differences are governance-relevant.

---

# 7. Governance-State Binding

The authorization binds references to the governance state relied upon when `ALLOW` was issued and binding occurred.

A reference set may include:

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

### Requirement

Governance-state references **MUST** be stable enough to support:

1. continuity evaluation; and
2. later decision provenance.

They may be:

- immutable snapshots;
- stable references to immutable records;
- content-addressed records;
- or equivalent tamper-evident state references.

---

# 8. Capability-State Binding

Example:

```text
capability_snapshot_id = CAP-491
```

This indicates which validated capability state supported admissibility.

### Requirement

The snapshot establishes historical governance basis.

It does not prove that operational capability remains unchanged later.

Where capability freshness is material, continuity may re-evaluate it.

---

# 9. Identity-State Binding

Example:

```text
identity_snapshot_id = ID-772
```

### Requirement

The authorization **MUST** preserve the identity/standing state relied upon when the authorization was created.

A historical identity snapshot does not automatically prove current standing.

---

# 10. Authority-State Binding

Example:

```text
authority_snapshot_id = AUTH-9841
```

The architecture requires the distinction:

```text
BoundAuthoritySnapshot
    ≠
CurrentAuthorityProof
```

### Requirement

The authorization records the authority state that justified the decision at binding time.

It does **not** convert that authority into permanent permission.

If authority changes materially before execution, continuity must respond.

---

# 11. Evidence-State Binding

Example:

```text
evidence_snapshot_id = EVD-225
```

### Requirement

The authorization should preserve the evidence state relied upon for governance.

Where an evidence manifest is material, an implementation may bind:

```text
EvidenceHash =
    H(Canonical(EvidenceManifest))
```

But:

```text
EvidenceWasValid_t0
    ≠
EvidenceIsValid_t1
```

Evidence freshness remains a continuity concern.

---

# 12. Scope and Delegation Binding

Example:

```text
scope_snapshot_id = SCP-773
```

### Requirement

The authorization **MUST NOT** silently broaden delegated scope.

An authorization produced under:

```text
vendor payments
up to ₹250,000
for Enterprise-A
```

does not imply authorization for:

```text
payroll payments
₹2,500,000
Enterprise-B
```

---

# 13. Policy Binding

Conceptually:

```json
{
  "policy_id": "TREASURY-PAYMENTS",
  "policy_version": 17
}
```

### Requirement

The policy identity and version relied upon during governance **MUST** be explicit enough for:

- continuity;
- provenance;
- audit reconstruction.

A later policy version does not retroactively change what policy governed the historical decision.

Whether the old policy remains valid for future execution is a continuity question.

---

# 14. Risk-State Binding

Example:

```text
risk_classification_id = RISK-442
risk_classification    = HIGH
```

### Requirement

The authorization should preserve the risk state that supported the governance decision.

If risk materially changes before execution, continuity may require:

```text
REVALIDATE
HOLD
ESCALATE
REJECT
```

---

# 15. Human Approval Binding

A human approval must not be represented only as:

```text
approved = true
```

Where approval is required, the authorization should bind an explicit approval record.

Example:

```text
approval_id = APR-118
```

The approval record should preserve, as applicable:

```text
approver_identity
approved_action_hash
approval_scope
approval_timestamp
approval_expiry
```

### Requirement

The approved action must match the bound action:

```text
ApprovedActionHash
    =
BoundActionHash
```

A human approval for one material action **MUST NOT** silently authorize a different material action.

---

# 16. Authorization Lifetime

The authorization defines:

```text
issued_at
expires_at
```

Example:

```text
issued_at  = 2026-08-24T08:00:00Z
expires_at = 2026-08-24T08:05:00Z
```

Validity requires:

```text
t_issue <= t_current < t_expiry
```

Otherwise:

```text
REJECT_EXPIRED
```

### Requirement

Authorization lifetime **MUST** be bounded.

A bound authorization is not an indefinite grant.

### TTL policy

The authorization lifetime may depend on:

```text
consequence
authority volatility
evidence volatility
risk
policy
approval lifetime
resource volatility
```

Conceptually:

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

---

# 17. Replay Semantics

The authorization must define whether it is:

```text
single_use
```

or another explicitly permitted usage mode.

For single-use authorization:

```text
Consume(AuthZ_id)
```

must be enforced atomically with the required execution semantics.

### Required behavior

```text
First valid use
    → allowed to proceed if all other checks pass

Second use
    → REPLAY_REJECTED
```

### Requirement

A consumed single-use authorization **MUST NOT** create another protected consequence.

This supports **C6 — Single-Use Where Required**.

---

# 18. Nonce / Unique Execution Token

A nonce or equivalent unique value may support replay detection.

Example:

```text
nonce = 9ecf...
```

### Requirement

The exact mechanism is implementation-specific.

The architectural requirement is that replay semantics be enforceable and reconstructable.

---

# 19. Authorization Integrity

Material authorization fields must be integrity protected.

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

Equivalent mechanisms may include:

- asymmetric signatures;
- signed tokens;
- keyed MACs;
- hardware-backed signatures;
- workload-attested authorization objects;
- other tamper-evident authorization envelopes.

### Requirement

> **Material authorization fields must not be modifiable without invalidating the authorization.**

If any material bound field is changed after issuance without valid reauthorization, integrity verification **MUST** fail.

---

# 20. Authorization Canonicalization

Before integrity protection, the authorization should have a deterministic canonical representation.

Canonicalization should define:

```text
field ordering
string normalization
Unicode normalization
numeric representation
timestamp representation
null semantics
optional-field semantics
array ordering
schema-version semantics
resource serialization
governance-state serialization
```

### Requirement

The same material authorization **MUST** produce the same canonical representation.

Materially different authorization objects **MUST NOT** collapse to the same representation because of ambiguous serialization.

---

# 21. Authorization State Machine

A conceptual authorization lifecycle is:

```text
ALLOW
  ↓
BINDING
  ├──→ ACTION_MISMATCH
  ├──→ RESOURCE_MISMATCH
  ├──→ EXECUTOR_UNRESOLVED
  ├──→ SNAPSHOT_FAILURE
  ├──→ APPROVAL_MISMATCH
  ├──→ SIGNING_FAILURE
  └──→ BOUND
          ↓
      CONTINUITY
```

Only:

```text
BOUND
```

may proceed to continuity evaluation.

### Requirement

A partially created or unverifiable authorization **MUST NOT** be treated as a valid `BOUND` authorization.

---

# 22. Binding Failure Codes

A reference implementation may use structured failure codes such as:

```text
ACTION_MISMATCH
RESOURCE_MISMATCH
EXECUTOR_UNRESOLVED
GOVERNANCE_STATE_UNRESOLVED
APPROVAL_MISMATCH
POLICY_BINDING_FAILURE
INVALID_VALIDITY_WINDOW
REPLAY_POLICY_INVALID
CANONICALIZATION_FAILURE
SIGNING_FAILURE
INTEGRITY_SETUP_FAILURE
```

These codes are illustrative.

The architecture requires explicit failure states rather than silent degradation.

---

# 23. Exact Action Verification

At enforcement, the execution action is re-canonicalized:

```text
ExecutionActionHash =
    H(
        Canonical(Action_execution)
    )
```

and compared with:

```text
BoundActionHash
```

Required relation:

```text
ExecutionActionHash
    =
BoundActionHash
```

Otherwise:

```text
ACTION_MISMATCH
    → REJECT
```

---

# 24. Executor Verification

At enforcement:

```text
Executor_execution
    =
Executor_bound
```

must hold.

Otherwise:

```text
EXECUTOR_MISMATCH
    → REJECT
```

### Requirement

Possession of the authorization object alone must not automatically imply the right to execute it where executor binding is required.

---

# 25. Resource Verification

At enforcement:

```text
Resource_execution
    =
Resource_bound
```

must hold.

Otherwise:

```text
RESOURCE_MISMATCH
    → REJECT
```

---

# 26. Authorization Expiry Verification

At execution:

```text
issued_at <= current_time < expires_at
```

must hold.

Otherwise:

```text
AUTHORIZATION_EXPIRED
    → REJECT
```

---

# 27. Replay Verification

For single-use semantics:

```text
Consumed(AuthZ_id)
    =
FALSE
```

must hold before commit.

Consumption should occur atomically with the protected mutation semantics required by the implementation.

---

# 28. Authorization Does Not Create Permanent Permission

This is a central architectural boundary.

```text
Bound_t0
    ≠>
NecessarilyExecutable_t1
```

Binding proves:

> **what was authorized**

It does not prove:

> **that it remains legitimate to execute now**

Between `t0` and `t1`:

- authority may be revoked;
- evidence may become stale;
- policy may change;
- approval may expire;
- risk may change;
- the resource may change;
- the executor may lose standing;
- the authorization may expire;
- the authorization may be consumed.

Those are evaluated through the Continuity Contract.

---

# 29. Relationship to the Continuity Contract

The execution authorization provides the historical governance basis against which current state is evaluated.

Conceptually:

```text
Execution Authorization
        +
Current Governance State
        +
Current Execution Context
        ↓
Continuity Evaluation
```

The continuity contract should determine whether the authorization state becomes:

```text
VALID
REVALIDATE
HOLD
ESCALATE
REJECT
```

The authorization object itself **MUST NOT** claim that historical governance state remains current.

---

# 30. Relationship to Enforcement

The enforcement boundary receives:

```text
action
+
execution authorization
+
continuity result
+
current execution context
```

Conceptually:

```text
ProtectedMutation(
    action,
    authorization,
    execution_context
)
```

The authorization therefore acts as a proof-bearing input to enforcement.

It is not enforcement by itself.

---

# 31. Relationship to Provenance

The execution authorization is a key provenance artifact.

A later provenance chain should be able to reconstruct:

```text
action_id
    ↓
decision_id
    ↓
authorization_id
    ↓
continuity_check_id
    ↓
enforcement_id
    ↓
execution_id
    ↓
outcome_id
```

The authorization must therefore preserve stable identifiers and governance-state references sufficient for later reconstruction.

---

# 32. Relationship to Architectural Invariants

The execution authorization directly supports:

## C3 — Exact Action Binding

```text
ExecutionActionHash
    =
BoundActionHash
```

The authorization binds one material action.

---

## C4 — Executor Binding

```text
Executor_current
    =
Executor_bound
```

The authorization identifies the permitted executor.

---

## C5 — Bounded Authorization Lifetime

```text
t_issue <= t_current < t_expiry
```

The authorization has an explicit validity window.

---

## C6 — Single-Use Where Required

```text
Consumed(AuthZ_id)
    =>
NOT Reusable(AuthZ_id)
```

The authorization carries enforceable replay semantics.

---

## C7 / C8 — Continuity

The authorization preserves the governance state against which current validity can later be evaluated.

It does not itself satisfy continuity.

---

## C9 / C10 — Enforcement

The authorization provides required execution proof.

Enforcement must still verify it and fail closed when required proof is invalid or unverifiable.

---

## C13 / C14 / C15 — Provenance

The authorization is one stable link in the decision-to-execution chain and must remain reconstructable and integrity protected.

---

# 33. Bank-Transfer Reference Authorization

Reference action:

```text
Transfer ₹250,000
from Corporate-Account-01
to Vendor-ABC
```

Corresponding authorization:

```json
{
  "schema_version": "0.1",
  "authorization_id": "AUTHZ-7F92",
  "decision_id": "DEC-1882",
  "action_id": "ACT-2041",
  "actor_id": "Treasury-Agent-01",
  "executor_id": "Payments-Service-Prod",
  "action_type": "transfer_funds",
  "action_hash": "sha256:...",
  "resource": {
    "resource_type": "bank_account",
    "resource_id": "Corporate-Account-01",
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
      "policy_id": "TREASURY-PAYMENTS",
      "policy_version": 17
    },
    "risk_classification_id": "RISK-442",
    "risk_classification": "HIGH",
    "approval_id": "APR-118"
  },
  "issued_at": "2026-08-24T08:00:00Z",
  "expires_at": "2026-08-24T08:05:00Z",
  "usage": {
    "mode": "single_use",
    "nonce": "9ecf..."
  },
  "integrity": {
    "algorithm": "implementation-defined",
    "signature": "..."
  }
}
```

This authorization means:

> **Payments-Service-Prod is eligible to execute this exact governed transfer, against the bound resource and governance-state basis, within the defined validity window and replay policy, subject to continuity and enforcement.**

It does **not** mean:

> **Execute immediately regardless of current state.**

---

# 34. Example Failure Scenarios

## Case A — Amount modified

Bound:

```text
₹250,000
```

Execution request:

```text
₹350,000
```

Result:

```text
ACTION_MISMATCH
    → REJECT
```

---

## Case B — Beneficiary modified

Bound:

```text
Vendor-ABC
```

Execution:

```text
Vendor-XYZ
```

Result:

```text
ACTION_MISMATCH
    → REJECT
```

---

## Case C — Wrong executor

Bound:

```text
Payments-Service-Prod
```

Presented by:

```text
Legacy-Payments-Service
```

Result:

```text
EXECUTOR_MISMATCH
    → REJECT
```

---

## Case D — Wrong protected resource

Bound:

```text
Corporate-Account-01
```

Execution:

```text
Corporate-Account-02
```

Result:

```text
RESOURCE_MISMATCH
    → REJECT
```

---

## Case E — Authorization expired

```text
expires_at = 08:05:00Z
execution  = 08:05:01Z
```

Result:

```text
AUTHORIZATION_EXPIRED
    → REJECT
```

---

## Case F — Authorization replay

First use:

```text
AUTHZ-7F92
    → COMMITTED
```

Second use:

```text
AUTHZ-7F92
    → REPLAY_REJECTED
```

---

## Case G — Authorization tampered

Original:

```text
resource_id = Corporate-Account-01
```

Modified after signing:

```text
resource_id = Corporate-Account-02
```

Result:

```text
INTEGRITY_INVALID
    → REJECT
```

---

## Case H — Authority revoked after binding

At binding:

```text
authority = CURRENT
```

Before execution:

```text
authority = REVOKED
```

The authorization object itself may still be cryptographically valid.

But continuity must return an execution-blocking result.

For example:

```text
REJECT
```

This demonstrates:

```text
Authorization Integrity Valid
    ≠
Execution Legitimacy Current
```

---

# 35. Reference Validation Sequence

A conceptual authorization verification sequence is:

```text
1. Parse authorization
2. Verify schema version
3. Verify authorization integrity
4. Verify authorization identifier
5. Verify decision linkage
6. Verify action identifier
7. Recompute action hash
8. Verify action hash match
9. Verify resource binding
10. Verify executor binding
11. Verify authorization lifetime
12. Verify replay state
13. Resolve continuity result
14. Verify commit-time conditions
15. Permit enforcement decision
```

The exact ordering may vary by implementation.

However, every legitimacy-affecting check required for the consequence class must complete before protected mutation.

---

# 36. Reference Pseudocode

Conceptually:

```text
ValidateExecutionAuthorization(
    authorization,
    execution_action,
    execution_context
):

    require authorization schema supported

    require VerifyIntegrity(authorization)

    require authorization.action_id
        == execution_action.action_id

    require authorization.action_hash
        == Hash(Canonical(execution_action))

    require authorization.resource
        == execution_context.resource

    require authorization.executor_id
        == execution_context.executor_id

    require authorization.issued_at
        <= execution_context.current_time
        < authorization.expires_at

    require authorization not consumed

    return AUTHORIZATION_VALID
```

Important:

```text
AUTHORIZATION_VALID
    ≠
COMMIT
```

The next stage is continuity.

---

# 37. Authorization Validation Outcomes

A reference implementation may use:

```text
AUTHORIZATION_VALID
INVALID_SCHEMA
INTEGRITY_INVALID
DECISION_LINK_INVALID
ACTION_ID_MISMATCH
ACTION_MISMATCH
RESOURCE_MISMATCH
EXECUTOR_MISMATCH
AUTHORIZATION_NOT_YET_VALID
AUTHORIZATION_EXPIRED
REPLAY_REJECTED
GOVERNANCE_REFERENCE_INVALID
APPROVAL_BINDING_INVALID
```

A failure should be explicit and provenance-capable.

---

# 38. Security Considerations

Authorization objects are security-relevant because protected execution may depend upon them.

Implementations should consider:

- signature forgery;
- token theft;
- bearer-token misuse;
- executor impersonation;
- confused deputy problems;
- action substitution;
- resource substitution;
- tenant substitution;
- environment substitution;
- schema downgrade;
- canonicalization disagreement;
- replay;
- race conditions;
- authorization duplication;
- stale authority;
- stale approval;
- key rotation;
- signing-key compromise;
- validation-library inconsistencies;
- time-source integrity.

---

# 39. Time Source Considerations

Authorization expiry depends on time.

Implementations should define an authoritative time source appropriate to the consequence class.

### Requirement

A component **MUST NOT** silently extend authorization lifetime because its local clock is stale or unreliable.

For high-consequence execution, trusted time semantics may become part of the enforcement boundary.

---

# 40. Key and Trust Considerations

The architecture does not prescribe one signing mechanism.

However, the enforcement point must know:

```text
which issuer is trusted
which key / attestation is valid
which schema is accepted
which authorization class is permitted
```

A correctly signed authorization from an untrusted issuer must not automatically become valid execution proof.

---

# 41. Issuer Identity

An implementation may include:

```text
issuer_id
```

or equivalent trust metadata.

Example:

```json
{
  "issuer_id": "Governance-Binding-Service-Prod"
}
```

### Requirement

Where multiple issuers exist, enforcement should be able to determine whether the authorization was issued by an accepted governance authority.

---

# 42. Optional Authorization Class

An implementation may include a field such as:

```text
authorization_class
```

Examples:

```text
financial_transfer
privileged_access
production_change
customer_data_mutation
```

This can help enforcement apply consequence-specific validation policy.

It must not replace exact action binding.

---

# 43. Optional Resource Version Binding

For high-consequence operations, the authorization may bind a resource version:

```text
resource_version = 481
```

At commit:

```text
CurrentResourceVersion
    =
BoundExpectedVersion
```

may be required.

A mismatch can trigger:

```text
REVALIDATE
```

or:

```text
REJECT
```

depending on policy.

This supports continuity and transaction-safe execution.

---

# 44. Optional State Preconditions

An authorization may bind explicit execution preconditions.

Example:

```json
{
  "preconditions": [
    {
      "type": "resource_state",
      "expected": "ACTIVE"
    }
  ]
}
```

### Requirement

If preconditions are used, they must be deterministic and enforceable.

They must not become a hidden substitute for the Continuity Contract.

---

# 45. Optional Delegation Metadata

Where execution authority is delegated across services, the authorization may include:

```text
delegation_chain_id
delegating_principal
delegation_scope
delegation_expiry
```

### Requirement

Delegation must not silently expand action, resource, executor, or authority scope.

---

# 46. Serialization Independence

The contract semantics may be implemented as:

- JSON;
- protobuf;
- signed JWT-like objects;
- CBOR;
- typed internal objects;
- database records;
- capability tokens;
- workload-attested structures;
- or another deterministic representation.

The architecture is not tied to JSON.

What matters is preservation of the defined control semantics.

---

# 47. What the Execution Authorization Proves

A valid bounded authorization can establish that, at binding time:

- a specific `ALLOW` decision existed;
- it applied to a specific action;
- that action had a stable material representation;
- a specific protected resource was bound;
- a specific executor was bound;
- specific governance-state references were relied upon;
- a specific policy version applied;
- a required approval was bound where applicable;
- the authorization had a bounded lifetime;
- replay semantics were defined;
- material authorization fields were integrity protected.

---

# 48. What the Execution Authorization Does Not Prove

The authorization does **not** itself prove:

- that authority remains current;
- that evidence remains valid;
- that approval has not been revoked;
- that policy remains applicable;
- that risk has not materially changed;
- that the protected resource is still in a compatible state;
- that the executor remains trusted;
- that an alternate route does not exist;
- that enforcement will actually occur;
- that the protected mutation succeeded;
- or that the resulting consequence was legitimate.

These are addressed by:

```text
Continuity
Enforcement
Route Closure
Execution
Decision Provenance
```

---

# 49. Test Intent

The future implementation should include tests covering at least:

```text
test_allow_can_produce_bound_authorization()

test_non_allow_cannot_produce_authorization()

test_modified_action_rejected()

test_wrong_resource_rejected()

test_wrong_executor_rejected()

test_expired_authorization_rejected()

test_consumed_authorization_not_reusable()

test_tampered_authorization_rejected()

test_decision_linkage_required()

test_governance_state_references_preserved()

test_approval_hash_matches_bound_action()

test_authorization_does_not_bypass_continuity()
```

These names are illustrative until the code structure is finalized.

---

# 50. Specification Boundary

This specification defines the **bounded execution authorization**.

It stops before current-state revalidation.

The next contract defines:

> **whether the governance conditions supporting this authorization still hold when the system is ready to create consequence.**

That contract is:

```text
continuity-contract.md
```

---

# 51. Specification Status

```text
Reference Architecture v0.1       COMPLETE
Architectural Invariants C1–C15   COMPLETE
Action Contract v0.1              COMPLETE
Execution Authorization v0.1      COMPLETE
Continuity Contract               NEXT
Enforcement Contract              PLANNED
Provenance Contract               PLANNED
Bank-Transfer Specification       PLANNED
Executable Tests                  PLANNED
```

This document is the canonical execution-authorization specification for **The Agentic AI Governance Control Plane v0.1**.

---

## Related Specifications

- [Architectural Invariants](invariants.md)
- [Action Contract](action-contract.md)
- [Reference Architecture Index](../docs/README.md)
- [Section 3 — Runtime Admissibility](../docs/03-runtime-admissibility.md)
- [Section 4 — Binding](../docs/04-binding.md)
- [Section 5 — Continuity](../docs/05-continuity.md)
- [Section 6 — Enforcement](../docs/06-enforcement.md)

**Return to Main Repository:** [The Agentic AI Governance Control Plane](../README.md)
