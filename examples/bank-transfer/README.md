# Bank Transfer Reference Scenario

## Practical Implementation Guide for The Agentic AI Governance Control Plane

**Version:** v0.1  
**Status:** Complete Reference Scenario Specification  
**Scenario Type:** High-Consequence Financial Action  
**Reference Action:** Transfer ₹250,000 from `Corporate-Account-01` to `Vendor-ABC`

---

## 1. Purpose

This document is the practical, end-to-end reference scenario for **The Agentic AI Governance Control Plane**.

It translates the architecture defined in Sections 1–8 and the supporting specification contracts into one concrete consequence-bearing enterprise action:

> **Treasury-Agent-01 proposes a ₹250,000 transfer from Corporate-Account-01 to Vendor-ABC.**

The purpose of this scenario is not to demonstrate banking software.

It is to demonstrate how the governance architecture behaves when an AI-originated action can create a real enterprise consequence.

The scenario therefore follows the complete control chain:

```text
Proposed Action
      ↓
Runtime Admissibility
      ↓
Binding
      ↓
Execution Authorization
      ↓
Continuity
      ↓
Enforcement
      ↓
Route Closure
      ↓
Execution
      ↓
Decision Provenance
```

This document is intended to become the bridge between:

```text
Architecture
      ↓
Specifications
      ↓
Reference Implementation
      ↓
Executable Tests
```

---

# 2. Where This Scenario Fits in the Repository

This reference scenario should live here:

```text
examples/
└── bank-transfer/
    └── README.md
```

Recommended repository structure:

```text
/
├── README.md
├── docs/
│   ├── README.md
│   ├── 01-why-this-capstone-exists.md
│   ├── 02-from-model-risk-to-consequence-risk.md
│   ├── 03-runtime-admissibility.md
│   ├── 04-binding.md
│   ├── 05-continuity.md
│   ├── 06-enforcement.md
│   ├── 07-route-closure.md
│   └── 08-decision-provenance.md
│
├── specifications/
│   ├── invariants.md
│   ├── action-contract.md
│   ├── execution-authorization.md
│   ├── continuity-contract.md
│   ├── enforcement-contract.md
│   └── provenance-contract.md
│
├── examples/
│   └── bank-transfer/
│       └── README.md
│
├── src/
├── tests/
├── diagrams/
├── LICENSE
└── .gitignore
```

The `docs/` directory defines **why the architecture exists and how it works**.

The `specifications/` directory defines **the contracts and invariants an implementation must satisfy**.

The `examples/bank-transfer/` directory demonstrates **how those architectural and contractual obligations behave together in one end-to-end use case**.

The future code implementation for this scenario may later be placed under:

```text
src/
```

with corresponding executable verification under:

```text
tests/
```

The scenario document itself should remain in:

```text
examples/bank-transfer/README.md
```

because it is an implementation-facing reference example rather than another architecture section.

---

# 3. Architectural Basis

The reference architecture begins from one governing question:

> **Can this action, by this actor, under these conditions, become real now?**

The bank-transfer scenario applies that question to:

```text
Action:
Transfer ₹250,000

Actor:
Treasury-Agent-01

Source Resource:
Corporate-Account-01

Beneficiary:
Vendor-ABC

Environment:
production
```

The scenario is intentionally consequence-bearing.

A transfer of funds is not merely model output.

It is a proposed state transition:

```text
Corporate-Account-01 balance
        ↓
Protected mutation
        ↓
External financial consequence
```

The architecture therefore requires governance before consequence.

---

# 4. Scenario Objective

The implementation must demonstrate that an AI-originated payment cannot become real merely because:

```text
the model recommends it
```

or because:

```text
the agent can call a payment tool
```

or because:

```text
the actor authenticated successfully
```

or because:

```text
an ALLOW decision existed earlier
```

or because:

```text
a valid authorization object exists
```

or because:

```text
the primary payment API is governed
```

Instead, the protected transfer should occur only when the complete required governance path remains satisfied.

The reference scenario therefore demonstrates:

```text
Desirable
    ≠
Admissible

Admissible
    ≠
Executable

Bound
    ≠
Still Valid

Governance Decision
    ≠
Enforcement

Governed Primary Route
    ≠
Governed System

Logging
    ≠
Decision Provenance
```

---

# 5. Actors and Components

## 5.1 Treasury Agent

```text
actor_id = Treasury-Agent-01
actor_type = ai_agent
```

Role:

- interprets treasury work;
- retrieves payment evidence;
- prepares a proposed transfer;
- submits a structured Action Contract.

The Treasury Agent does **not** directly possess protected mutation privilege.

It is the **requesting AI actor**, not the originating source of execution authority.

---

## 5.2 Authority Principal and Authority Basis

Reference identities:

```text
authority_principal_id = Treasury-Manager-17
authority_basis_id     = TREASURY-MANDATE-01
```

For this scenario, the governed treasury mandate is the authority basis from which permission for the proposed transfer is derived.

The authority principal, requesting AI actor, authorization issuer, and authorized executor are intentionally represented as distinct roles:

```text
Authority Principal:
    Treasury-Manager-17

Authority Basis:
    TREASURY-MANDATE-01

Requesting AI Actor:
    Treasury-Agent-01

Authorization Issuer:
    Governance-Binding-Service-Prod

Authorized Executor:
    Payments-Service-Prod
```

The execution authorization does not create the authority basis. It is a bounded permission artifact derived from governance state that includes that basis.

---

## 5.3 Governance Control Plane

Responsible for:

```text
Admissibility
Binding
Continuity
Governance-state evaluation
```

It evaluates whether the proposed transfer is eligible to progress.

---

## 5.4 Binding / Authorization Service

Example identity:

```text
Governance-Binding-Service-Prod
```

Responsible for producing the bounded execution authorization after an `ALLOW` decision.

For this scenario:

```text
authorization_issuer_id = Governance-Binding-Service-Prod
```

The issuer produces the authorization artifact. It is not thereby the source of the authority or standing represented by that artifact.

---

## 5.5 Payments Service

```text
executor_id = Payments-Service-Prod
```

The privileged executor authorized to create the financial consequence.

The actor that proposes the action and the executor that creates the consequence are intentionally distinct.

---

## 5.6 Enforcement Point

Example:

```text
enforcement_point_id = PAYMENT-GATEWAY-01
```

Responsible for ensuring the Payments Service cannot commit the transfer unless required execution legitimacy is established.

---

## 5.7 Protected Resource

```text
resource_type = bank_account
resource_id   = Corporate-Account-01
tenant        = Enterprise-A
environment   = production
```

Protected state:

```text
Q = Corporate-Account-01 balance
```

---

## 5.8 Beneficiary

```text
beneficiary_id = Vendor-ABC
```

The beneficiary is a material part of the proposed action.

Changing the beneficiary changes the governed action.

---

## 5.9 Human Approver

Example:

```text
approver_id = Treasury-Manager-17
approval_id = APR-118
```

The scenario treats the ₹250,000 transfer as requiring explicit human approval because it is a high-consequence financial action.

---

# 6. Protected Consequence

The protected consequence is:

```text
debit Corporate-Account-01 by ₹250,000
credit / submit payment to Vendor-ABC
```

The architectural governance target is not merely:

```text
POST /payments
```

It is the protected enterprise state transition itself.

This distinction matters for Route Closure.

---

# 7. Initial Proposed Action

The intelligence layer produces a proposal:

```text
"Transfer ₹250,000 from Corporate-Account-01 to Vendor-ABC
against approved invoice INV-8842."
```

That statement is not executable authority.

The proposal must first become a structured Action Contract.

---

# 8. Action Contract

Reference Action Contract:

```json
{
  "schema_version": "0.1",
  "action_id": "ACT-2041",
  "actor": {
    "actor_id": "Treasury-Agent-01",
    "actor_type": "ai_agent",
    "tenant": "Enterprise-A",
    "environment": "production"
  },
  "domain": "treasury",
  "action_type": "transfer_funds",
  "resource": {
    "resource_type": "bank_account",
    "resource_id": "Corporate-Account-01"
  },
  "parameters": {
    "beneficiary_id": "Vendor-ABC",
    "amount_minor": 25000000,
    "currency": "INR",
    "payment_reference": "INV-8842"
  },
  "consequence": {
    "class": "financial",
    "impact": "high",
    "reversibility": "low",
    "persistence": "high"
  },
  "requested_at": "2026-08-24T08:00:00Z",
  "material_fields": [
    "actor.actor_id",
    "actor.tenant",
    "actor.environment",
    "action_type",
    "resource.resource_type",
    "resource.resource_id",
    "parameters.beneficiary_id",
    "parameters.amount_minor",
    "parameters.currency",
    "parameters.payment_reference"
  ]
}
```

The action is canonicalized:

```text
CanonicalAction =
    Canonicalize(ActionContract)
```

and hashed:

```text
ActionHash =
    H(CanonicalAction)
```

Illustrative reference:

```text
action_hash = sha256:ACTION-2041-HASH
```

---

# 9. Why the Action Contract Matters

The Action Contract establishes exactly what governance is evaluating.

Without it, the system might govern:

```text
"make vendor payment"
```

while executing:

```text
₹250,000
Corporate-Account-01
→ Vendor-ABC
```

or something materially different.

The Action Contract provides the comparison surface required for:

```text
C1 — No Direct Consequence
C2 — No Inherited Admissibility
C3 — Exact Action Binding
```

---

# 10. Action Materiality

For this scenario, the following are material:

```text
actor_id
tenant
environment
action_type
source account
beneficiary
amount
currency
payment reference
```

Examples:

```text
₹250,000 → ₹350,000
```

is material.

```text
Vendor-ABC → Vendor-XYZ
```

is material.

```text
Corporate-Account-01 → Corporate-Account-02
```

is material.

```text
staging → production
```

is material.

A display label change may be non-material if it cannot alter execution semantics.

---

# 11. Entry into Runtime Admissibility

After structural validation:

```text
ActionContract = VALID
```

The action enters runtime admissibility.

The governing function is:

```text
Γ(a_t, G_t) ∈ {
    ALLOW,
    HOLD,
    ESCALATE,
    DENY
}
```

For this scenario, governance state includes:

```text
Capability
Identity / Standing
Authority
Evidence / Context
Scope / Delegation
Policy
Risk / Reversibility
Human Approval
```

---

# 12. Admissibility State at t0

Reference state:

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

Supporting references:

```text
capability_snapshot_id = CAP-491
identity_snapshot_id   = ID-772
authority_snapshot_id  = AUTH-9841
evidence_snapshot_id   = EVD-225
scope_snapshot_id      = SCP-773
policy_id              = TREASURY-PAYMENTS
policy_version         = 17
risk_classification_id = RISK-442
approval_id            = APR-118
```

---

# 13. Capability Gate

The Treasury Agent must have validated capability for the action class.

Reference state:

```text
Capability = VALID
```

Capability may be based on implementation-defined evidence such as:

```text
payment-task evaluation
tool-use correctness
recent operational performance
environment eligibility
consequence class
```

Important:

```text
Capability ≠ Authority
```

An agent may be capable of preparing a payment but still lack authority to cause the transfer.

---

# 14. Identity and Standing Gate

Reference:

```text
actor_id = Treasury-Agent-01
standing = CURRENT
```

The system establishes that the requesting actor is currently recognized.

Authentication alone is not sufficient.

---

# 15. Authority Gate

Reference:

```text
authority_principal_id = Treasury-Manager-17
authority_basis_id     = TREASURY-MANDATE-01
authority_snapshot_id  = AUTH-9841
authority              = CURRENT
```

For this scenario, the authority basis permits the governed delegation under which:

```text
Treasury-Agent-01
may request vendor payment initiation
for Corporate-Account-01
within Enterprise-A / production
```

The requesting AI actor is therefore not treated as the originating source of authority.

The authority basis, requesting actor, authorization issuer, and authorized executor remain separately identifiable.

Historical authority does not automatically mean current authority.

---

# 16. Evidence Gate

Reference evidence:

```text
Invoice INV-8842 = APPROVED
Vendor-ABC       = VERIFIED
Payment status   = UNPAID
```

Reference state:

```text
Evidence = SUFFICIENT
```

The evidence is separately governed because:

```text
Correct Reasoning ≠ Valid Evidence
```

---

# 17. Scope Gate

Assume current delegation permits:

```text
Treasury-Agent-01
may initiate approved vendor payments
up to ₹500,000
```

The proposed transfer is:

```text
₹250,000
```

Therefore:

```text
Scope = IN_SCOPE
```

---

# 18. Policy Gate

Reference:

```text
policy_id      = TREASURY-PAYMENTS
policy_version = 17
policy_state   = SATISFIED
```

Illustrative policy requirements:

```text
verified beneficiary
approved invoice
sufficient authority
human approval for high-consequence transfer
current resource state
single-use authorization
```

These are scenario assumptions, not universal banking rules.

---

# 19. Risk and Reversibility Gate

Reference:

```text
Risk = HIGH
```

because the action is:

```text
financial
externally meaningful
low reversibility
persistent
```

The architecture therefore reduces unreviewed autonomy and requires human approval in this reference scenario.

---

# 20. Human Approval Gate

Reference approval:

```text
approval_id = APR-118
approver    = Treasury-Manager-17
status      = PRESENT
```

The approval binds to:

```text
approved_action_hash = sha256:ACTION-2041-HASH
amount               = ₹250,000
beneficiary          = Vendor-ABC
source account       = Corporate-Account-01
```

The approval does not mean:

```text
approve any future payment
```

It means:

```text
approve this material action
```

---

# 21. Admissibility Decision

All required governance conditions are valid.

Therefore:

```text
DEC-1882 = ALLOW
```

Important:

```text
ALLOW ≠ EXECUTE
```

The meaning is:

```text
ALLOW = ELIGIBLE_FOR_BINDING
```

---

# 22. Admissibility Decision Record

Conceptual record:

```json
{
  "decision_id": "DEC-1882",
  "action_id": "ACT-2041",
  "action_hash": "sha256:ACTION-2041-HASH",
  "decision_effect": "ALLOW",
  "decision_timestamp": "2026-08-24T08:00:20Z",
  "governance_state": {
    "capability": "VALID",
    "identity": "CURRENT",
    "authority": "CURRENT",
    "evidence": "SUFFICIENT",
    "scope": "IN_SCOPE",
    "policy": "SATISFIED",
    "risk": "HIGH",
    "approval": "PRESENT"
  },
  "reason_codes": [
    "CAPABILITY_VALID",
    "IDENTITY_CURRENT",
    "AUTHORITY_CURRENT",
    "EVIDENCE_SUFFICIENT",
    "SCOPE_IN_SCOPE",
    "POLICY_SATISFIED",
    "APPROVAL_PRESENT"
  ]
}
```

---

# 23. Binding

Binding converts:

```text
DEC-1882 = ALLOW
```

into a bounded authorization for:

```text
this exact action
this exact resource
this exact executor
this governance-state basis
this validity window
this replay policy
```

The authorization derives from the governed authority basis and does not itself create standing:

```text
Treasury-Manager-17 / TREASURY-MANDATE-01
        ↓
Authority State
        ↓
Treasury-Agent-01 requests
        ↓
Governance-Binding-Service-Prod issues
        ↓
Payments-Service-Prod may execute
```

The authorization statement becomes:

> **Payments-Service-Prod may execute this exact ₹250,000 transfer from Corporate-Account-01 to Vendor-ABC, based on the validated governance state identified in this authorization, before expiry, and subject to continuity validation.**

---

# 24. Execution Authorization

Reference:

```json
{
  "schema_version": "0.1",
  "authorization_id": "AUTHZ-7F92",
  "decision_id": "DEC-1882",
  "action_id": "ACT-2041",
  "actor_id": "Treasury-Agent-01",
  "authority_principal_id": "Treasury-Manager-17",
  "authority_basis_id": "TREASURY-MANDATE-01",
  "authorization_issuer_id": "Governance-Binding-Service-Prod",
  "executor_id": "Payments-Service-Prod",
  "action_type": "transfer_funds",
  "action_hash": "sha256:ACTION-2041-HASH",
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
  "issued_at": "2026-08-24T08:00:30Z",
  "expires_at": "2026-08-24T08:05:30Z",
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

---

# 25. Binding Guarantees

This authorization establishes:

```text
Requesting Actor
Authority Principal / Basis Reference
Authorization Issuer
Exact Action
Resource
Authorized Executor
Governance-State References
Policy Version
Approval Reference
Validity Window
Replay Semantics
Integrity Protection
```

It does not establish authority by its own existence. It carries bounded permission derived from the independently governed authority basis represented in the governance state.

It supports:

```text
C3 — Exact Action Binding
C4 — Executor Binding
C5 — Bounded Authorization Lifetime
C6 — Single-Use Where Required
```

But:

```text
Bound_t0
    ≠>
NecessarilyExecutable_t1
```

The next stage is Continuity.

---

# 26. Time Gap Before Execution

Assume execution is not immediate.

```text
Binding time:
08:00:30Z
```

Execution preparation occurs at:

```text
08:02:30Z
```

During that interval, enterprise state may have changed.

The implementation must not convert:

```text
historical validity
```

into:

```text
present permission
```

without checking continuity.

---

# 27. Continuity Evaluation

Reference function:

```text
K_t1 =
    Continuity(
        AUTHZ-7F92,
        G_t0,
        G_t1,
        ExecutionContext_t1
    )
```

Reference continuity state at `t1`:

```text
Identity        = CURRENT
Authority       = CURRENT
Evidence        = CURRENT
Scope           = IN_SCOPE
Policy          = SATISFIED
Risk            = HIGH
Approval        = PRESENT
Executor        = VALID / MATCH
Resource        = ACTIVE
Authorization  = UNEXPIRED
Replay State    = UNUSED
```

Result:

```text
CONT-441 = VALID
```

---

# 28. Continuity Record

```json
{
  "schema_version": "0.1",
  "continuity_check_id": "CONT-441",
  "authorization_id": "AUTHZ-7F92",
  "action_id": "ACT-2041",
  "evaluated_at": "2026-08-24T08:02:30Z",
  "result": "VALID",
  "dimensions": {
    "identity": "CURRENT",
    "authority": "CURRENT",
    "evidence": "CURRENT",
    "scope": "IN_SCOPE",
    "policy": "SATISFIED",
    "risk": "UNCHANGED_ACCEPTABLE",
    "approval": "PRESENT",
    "executor": "VALID",
    "resource": "COMPATIBLE",
    "temporal_validity": "VALID",
    "replay_state": "UNUSED"
  },
  "material_changes": [],
  "reason_codes": [],
  "requires_commit_revalidation": true
}
```

---

# 29. Commit-Time Revalidation

A continuity check itself may become stale.

Immediately before protected mutation, the implementation performs final checks.

Reference commit-time state:

```text
Action hash             = MATCH
Executor                = MATCH
Authorization           = UNEXPIRED
Replay State            = UNUSED
Authority               = CURRENT
Approval                = PRESENT
Evidence                = CURRENT
Policy                  = VALID
Resource                = ACTIVE
Risk                    = ACCEPTABLE
```

Only then may the action approach enforcement.

This operationalizes:

```text
C7 — Governance Continuity
C8 — Commit-Time Revalidation
```

---

# 30. Enforcement Request

The protected execution request arrives at:

```text
Payments-Service-Prod
```

through:

```text
PAYMENT-GATEWAY-01
```

Conceptual request:

```json
{
  "schema_version": "0.1",
  "enforcement_id": "ENF-992",
  "action_id": "ACT-2041",
  "authorization_id": "AUTHZ-7F92",
  "continuity_check_id": "CONT-441",
  "enforcement_point_id": "PAYMENT-GATEWAY-01",
  "execution_context": {
    "executor_id": "Payments-Service-Prod",
    "resource_id": "Corporate-Account-01",
    "tenant": "Enterprise-A",
    "environment": "production",
    "current_time": "2026-08-24T08:02:31Z"
  }
}
```

---

# 31. Enforcement Verification

The enforcement point verifies:

```text
Authorization integrity         = VALID
Authorization ID                = VALID
Decision linkage                = VALID
Action ID                       = MATCH
Action hash                     = MATCH
Resource                       = MATCH
Executor                       = MATCH
Tenant                         = MATCH
Environment                    = MATCH
Authorization lifetime         = VALID
Replay state                   = UNUSED
Continuity                     = VALID
Commit-time state              = VALID
```

Enforcement result:

```text
ENF-992 = COMMIT
```

Only `COMMIT` may admit the protected mutation.

---

# 32. Enforcement Is Not Advisory

The implementation must not behave as:

```text
Governance says REJECT
        ↓
Agent decides to continue anyway
```

The protected mutation interface should require governance proof.

Conceptually:

```text
ProtectedMutation(
    action,
    authorization,
    continuity_result,
    execution_context
)
```

not:

```text
ProtectedMutation(action)
```

This operationalizes:

```text
C9 — Enforcement Required
C10 — Fail Closed at Enforcement Boundary
```

---

# 33. Transactional Commit

A strong reference implementation should model the commit as a tightly controlled operation.

Conceptually:

```text
BEGIN TRANSACTION

verify AUTHZ-7F92
verify action hash
verify executor
verify resource
verify CONT-441
verify resource state
consume AUTHZ-7F92 if unused
apply transfer mutation
write execution receipt

COMMIT
```

If any required validation fails:

```text
ROLLBACK
```

---

# 34. Replay Protection

Single-use authorization:

```text
AUTHZ-7F92
```

must be consumed atomically.

Correct behavior:

```text
Request A
    ↓
ConsumeIfUnused(AUTHZ-7F92)
    ↓
SUCCESS

Request B
    ↓
ConsumeIfUnused(AUTHZ-7F92)
    ↓
REPLAY_REJECTED
```

Only one execution may use the single-use authorization.

---

# 35. Successful Execution

The protected mutation is performed:

```text
debit Corporate-Account-01
submit / credit payment to Vendor-ABC
consume AUTHZ-7F92
emit execution receipt
```

Reference:

```text
EXEC-881 = SUCCESS
```

---

# 36. Execution Receipt

```json
{
  "execution_id": "EXEC-881",
  "enforcement_id": "ENF-992",
  "authorization_id": "AUTHZ-7F92",
  "decision_id": "DEC-1882",
  "continuity_check_id": "CONT-441",
  "action_id": "ACT-2041",
  "action_hash": "sha256:ACTION-2041-HASH",
  "executor_id": "Payments-Service-Prod",
  "resource_id": "Corporate-Account-01",
  "commit_timestamp": "2026-08-24T08:02:32Z",
  "resource_version_before": 884,
  "resource_version_after": 885,
  "execution_result": "SUCCESS",
  "authorization_consumed": true
}
```

The execution receipt is not the complete provenance chain.

It is execution-layer evidence.

---

# 37. Route Closure

Enforcement proves that the governed route is controlled.

That is necessary but not sufficient.

The protected resource may be reachable through:

```text
Payments-Service-Prod
Legacy-Payments-API
Batch Settlement Job
Admin Console
Direct Database Procedure
Privileged Service Account
Emergency Operations Interface
```

Route Closure asks:

> **Can any identified consequence-bearing path create the same protected consequence without equivalent governance enforcement?**

---

# 38. Route Inventory

For this reference scenario, define:

```text
Q = Corporate-Account-01 balance
```

Illustrative identified routes:

```text
R1 — Governed Payments Service
R2 — Legacy Payments API
R3 — Batch Settlement Job
R4 — Administrative Payment Console
R5 — Direct Database Procedure
```

Each route must be classified.

---

# 39. Route R1 — Governed Route

```text
Treasury-Agent-01
      ↓
Governance Control Plane
      ↓
AUTHZ-7F92
      ↓
CONT-441
      ↓
PAYMENT-GATEWAY-01
      ↓
Payments-Service-Prod
      ↓
Corporate-Account-01
```

Status:

```text
CLOSED
```

because required governance enforcement applies.

---

# 40. Route R2 — Legacy API

Suppose:

```text
Treasury-Agent-01
      ↓
Legacy-Payments-API
      ↓
Corporate-Account-01
```

If this route can mutate the account without:

```text
valid authorization
continuity
equivalent enforcement
```

then:

```text
R2 = OPEN
```

and:

```text
RouteClosure(Corporate-Account-01)
    =
FALSE
```

even though R1 is perfectly governed.

---

# 41. Route Closure Remediation

An open route may be remediated by:

```text
disable legacy write route
```

or:

```text
place equivalent enforcement before legacy route
```

or:

```text
remove AI / service access to legacy credentials
```

or another implementation-specific control that creates equivalent governance effect.

Only after all identified AI-originated consequence-bearing routes are acceptably governed may the system declare:

```text
RouteClosure(Q) = TRUE
```

---

# 42. Route Closure Requirement

Plain-language invariant:

```text
FOR EVERY p IN P(A, Q):
    GovernedPath(p) = TRUE
```

Expanded:

```text
FOR EVERY p IN P(A, Q):
    EXISTS e IN p SUCH THAT
        EquivalentEnforcement(e) = TRUE
```

This operationalizes:

```text
C11 — Route Closure
C12 — No Alternate Consequence Path
```

---

# 43. Route Closure State for the Happy Path

For the completed reference scenario, assume all identified AI-originated routes have been remediated or equivalently governed.

Therefore:

```text
route_id            = R1
route_closure_state = CLOSED
```

This allows the successful execution to proceed within the declared architecture boundary.

---

# 44. Decision Provenance

After execution, the complete governance chain is:

```text
ACT-2041
      ↓
DEC-1882 = ALLOW
      ↓
APR-118 = PRESENT
      ↓
AUTHZ-7F92
      ↓
CONT-441 = VALID
      ↓
ENF-992 = COMMIT
      ↓
R1 = CLOSED
      ↓
EXEC-881 = SUCCESS
      ↓
OUT-557 = PAYMENT_ACCEPTED
```

This allows a reviewer to reconstruct the legitimacy of the consequence.

---

# 45. Provenance Record

```json
{
  "schema_version": "0.1",
  "provenance_id": "PROV-6601",
  "action": {
    "action_id": "ACT-2041",
    "action_type": "transfer_funds",
    "action_hash": "sha256:ACTION-2041-HASH",
    "resource_id": "Corporate-Account-01",
    "beneficiary_id": "Vendor-ABC",
    "amount_minor": 25000000,
    "currency": "INR"
  },
  "actor": {
    "actor_id": "Treasury-Agent-01",
    "tenant": "Enterprise-A",
    "environment": "production"
  },
  "governance_state": {
    "capability_snapshot_id": "CAP-491",
    "identity_snapshot_id": "ID-772",
    "authority_snapshot_id": "AUTH-9841",
    "evidence_snapshot_id": "EVD-225",
    "scope_snapshot_id": "SCP-773",
    "policy_id": "TREASURY-PAYMENTS",
    "policy_version": 17,
    "risk_classification": "HIGH",
    "approval_id": "APR-118"
  },
  "decision": {
    "decision_id": "DEC-1882",
    "decision_effect": "ALLOW"
  },
  "authorization": {
    "authorization_id": "AUTHZ-7F92",
    "bound_action_hash": "sha256:ACTION-2041-HASH",
    "bound_resource": "Corporate-Account-01",
    "bound_executor": "Payments-Service-Prod"
  },
  "continuity": {
    "continuity_check_id": "CONT-441",
    "continuity_result": "VALID"
  },
  "enforcement": {
    "enforcement_id": "ENF-992",
    "enforcement_point_id": "PAYMENT-GATEWAY-01",
    "enforcement_result": "COMMIT"
  },
  "route": {
    "route_id": "R1",
    "route_closure_state": "CLOSED"
  },
  "execution": {
    "execution_id": "EXEC-881",
    "execution_result": "SUCCESS",
    "resource_version_before": 884,
    "resource_version_after": 885,
    "authorization_consumed": true
  },
  "outcome": {
    "outcome_id": "OUT-557",
    "outcome_type": "PAYMENT_ACCEPTED"
  },
  "integrity": {
    "algorithm": "implementation-defined",
    "record_hash": "sha256:...",
    "signature": "..."
  }
}
```

---

# 46. Provenance Questions the Scenario Must Answer

A reviewer should be able to determine:

```text
What action was proposed?
Who proposed it?
Which governance state applied?
Why was it allowed?
Which approval applied?
What exactly was authorized?
Which executor was bound?
Was the authorization still current?
What continuity check was performed?
Which enforcement point admitted execution?
Which route was used?
Was that route closed?
What protected mutation occurred?
Was the authorization consumed?
What outcome followed?
Was the provenance record altered?
```

This operationalizes:

```text
C13 — Provenance Completeness
C14 — Decision-to-Execution Linkage
C15 — Provenance Integrity
```

---

# 47. Happy-Path End-to-End Sequence

```text
1. Treasury-Agent-01 proposes transfer
2. Action Contract ACT-2041 created
3. Action Contract structurally validated
4. Material action canonicalized
5. Action hash calculated
6. Runtime governance state evaluated
7. Human approval APR-118 verified
8. DEC-1882 = ALLOW
9. ALLOW enters Binding
10. AUTHZ-7F92 created
11. Authorization integrity protected
12. Authorization bound to Payments-Service-Prod
13. Continuity check CONT-441 performed
14. CONT-441 = VALID
15. Commit-time state revalidated
16. Enforcement point PAYMENT-GATEWAY-01 verifies authorization
17. ENF-992 = COMMIT
18. Route R1 verified within CLOSED route state
19. AUTHZ-7F92 atomically consumed
20. Protected transfer committed
21. EXEC-881 = SUCCESS
22. Execution receipt persisted
23. OUT-557 = PAYMENT_ACCEPTED
24. PROV-6601 assembled / linked
25. Provenance integrity protected
```

---

# 48. Failure Scenario A — No Governance

Attempt:

```text
Treasury-Agent-01
    ↓
direct payment tool
    ↓
Corporate-Account-01
```

Expected:

```text
BLOCK
```

because:

```text
No governance determination
```

Invariant:

```text
C1
```

---

# 49. Failure Scenario B — Material Action Change After ALLOW

Original:

```text
₹250,000
Vendor-ABC
```

Modified:

```text
₹350,000
Vendor-ABC
```

Expected:

```text
Previous ALLOW invalidated
```

The changed action requires fresh governance.

Invariant:

```text
C2
```

---

# 50. Failure Scenario C — Beneficiary Changed After Binding

Bound:

```text
Vendor-ABC
```

Execution:

```text
Vendor-XYZ
```

Expected:

```text
ACTION_MISMATCH
    → REJECT
```

Invariant:

```text
C3
```

---

# 51. Failure Scenario D — Wrong Executor

Bound executor:

```text
Payments-Service-Prod
```

Execution attempted by:

```text
Legacy-Payments-Service
```

Expected:

```text
EXECUTOR_MISMATCH
    → REJECT
```

Invariant:

```text
C4
```

---

# 52. Failure Scenario E — Authorization Expired

```text
expires_at = 08:05:30Z
attempt    = 08:05:31Z
```

Expected:

```text
REJECT_EXPIRED
```

Invariant:

```text
C5
```

---

# 53. Failure Scenario F — Replay

First use:

```text
AUTHZ-7F92
    → SUCCESS
    → CONSUMED
```

Second use:

```text
AUTHZ-7F92
    → REPLAY_REJECTED
```

Invariant:

```text
C6
```

---

# 54. Failure Scenario G — Authority Revoked After Binding

At binding:

```text
Authority = CURRENT
```

Before commit:

```text
Authority = REVOKED
```

Expected continuity:

```text
REJECT
```

No execution.

Invariant:

```text
C7
```

---

# 55. Failure Scenario H — Approval Revoked Before Commit

At binding:

```text
Approval = PRESENT
```

Before commit:

```text
Approval = REVOKED
```

Expected:

```text
CONTINUITY = REJECT
```

No transfer.

Invariants:

```text
C7
C8
```

---

# 56. Failure Scenario I — Evidence Becomes Stale

At binding:

```text
Vendor-ABC = VERIFIED
```

Before commit:

```text
Vendor-ABC = UNDER_REVIEW
```

Expected according to reference policy:

```text
HOLD
```

or:

```text
REVALIDATE
```

but never silent execution.

Invariant:

```text
C7
```

---

# 57. Failure Scenario J — Resource Frozen

At binding:

```text
Corporate-Account-01 = ACTIVE
```

Before commit:

```text
Corporate-Account-01 = FROZEN
```

Expected:

```text
REJECT
```

Invariant:

```text
C8
```

---

# 58. Failure Scenario K — Missing Continuity Proof

Authorization:

```text
VALID
```

Continuity result:

```text
MISSING
```

Expected enforcement:

```text
HOLD / REJECT
```

No protected mutation.

Invariants:

```text
C9
C10
```

---

# 59. Failure Scenario L — Invalid Authorization Signature

```text
Authorization integrity = INVALID
```

Expected:

```text
REJECT
```

No transfer.

Invariants:

```text
C9
C10
```

---

# 60. Failure Scenario M — Open Legacy Route

Primary governed route:

```text
R1 = CLOSED
```

Legacy route:

```text
R2 = OPEN
```

Expected system-level route closure:

```text
FALSE
```

The architecture must not claim:

```text
Governed System
```

merely because:

```text
Governed Primary Path
```

exists.

Invariants:

```text
C11
C12
```

---

# 61. Failure Scenario N — Missing Execution Receipt

Transfer succeeds, but:

```text
execution receipt = MISSING
```

Expected:

```text
Provenance = INCOMPLETE
```

This is an implementation failure requiring recovery / investigation.

Invariant:

```text
C13
```

---

# 62. Failure Scenario O — Broken Decision Link

Execution:

```text
EXEC-881
```

cannot resolve to:

```text
DEC-1882
```

Expected:

```text
PROVENANCE LINKAGE FAILURE
```

Invariant:

```text
C14
```

---

# 63. Failure Scenario P — Provenance Tampered

Original:

```text
amount = ₹250,000
```

Provenance modified after execution:

```text
amount = ₹25,000
```

Expected:

```text
INTEGRITY FAILURE DETECTED
```

Invariant:

```text
C15
```

---

# 64. Invariant-to-Scenario Mapping

| Invariant | Reference Scenario Proof |
|---|---|
| C1 — No Direct Consequence | Treasury Agent cannot directly mutate protected account state |
| C2 — No Inherited Admissibility | Material action/governance change invalidates prior ALLOW |
| C3 — Exact Action Binding | Execution action hash must equal bound action hash |
| C4 — Executor Binding | Only `Payments-Service-Prod` may use the authorization |
| C5 — Bounded Authorization Lifetime | Expired `AUTHZ-7F92` is rejected |
| C6 — Single-Use Where Required | Authorization can be consumed only once |
| C7 — Governance Continuity | Revocation/change blocks inherited execution permission |
| C8 — Commit-Time Revalidation | Required continuity must remain valid at commit |
| C9 — Enforcement Required | Payment mutation requires enforcement decision |
| C10 — Fail Closed | Missing/invalid/unverifiable proof cannot permit transfer |
| C11 — Route Closure | Every identified AI-originated payment path must be governed |
| C12 — No Alternate Consequence Path | Open legacy route makes route closure false |
| C13 — Provenance Completeness | Successful transfer leaves reconstructable lineage |
| C14 — Decision-to-Execution Linkage | `EXEC-881` resolves to decision/authorization/continuity/enforcement |
| C15 — Provenance Integrity | Material provenance tampering becomes detectable |

---

# 65. Contract-to-Scenario Mapping

## Action Contract

Defines:

```text
ACT-2041
```

and the exact proposed payment.

## Execution Authorization

Defines:

```text
AUTHZ-7F92
```

and binds the exact action, resource, executor, governance state, validity window, and replay semantics.

## Continuity Contract

Defines:

```text
CONT-441
```

and determines whether the authorization remains legitimate now.

## Enforcement Contract

Defines:

```text
ENF-992
```

and determines whether protected mutation may cross the commit boundary.

## Provenance Contract

Defines:

```text
PROV-6601
```

and reconstructs the full decision-to-outcome lineage.

---

# 66. Architecture Section Mapping

## Section 1 — Why This Capstone Exists

Applied here as the complete control chain:

```text
Proposed Action
→ Admissibility
→ Binding
→ Continuity
→ Enforcement
→ Route Closure
→ Execution
→ Provenance
```

## Section 2 — From Model Risk to Consequence Risk

The transfer moves the system from recommendation into enterprise state mutation.

The target is consequence governance, not merely model-output correctness.

## Section 3 — Runtime Admissibility

The transfer is evaluated against:

```text
Capability
Identity
Authority
Evidence
Scope
Policy
Risk
Approval
```

and produces:

```text
DEC-1882 = ALLOW
```

## Section 4 — Binding

The `ALLOW` decision becomes:

```text
AUTHZ-7F92
```

bound to the exact action, account, executor, state, lifetime, and replay policy.

## Section 5 — Continuity

The bound state is re-evaluated at the consequence boundary:

```text
CONT-441 = VALID
```

## Section 6 — Enforcement

The protected payment path requires:

```text
ENF-992 = COMMIT
```

before mutation.

## Section 7 — Route Closure

Alternate payment routes must be identified and equivalently governed.

## Section 8 — Decision Provenance

The full chain from:

```text
ACT-2041
```

to:

```text
OUT-557
```

must remain reconstructable.

---

# 67. State-Machine View

```text
PROPOSED
   ↓
ACTION_VALID
   ↓
EVALUATING
   ├── DENY
   ├── HOLD
   ├── ESCALATE
   └── ALLOW
          ↓
       BINDING
          ├── FAILURE
          └── BOUND
                 ↓
          CONTINUITY_CHECK
             ├── REJECT
             ├── HOLD
             ├── ESCALATE
             ├── REVALIDATE
             └── VALID
                    ↓
              ENFORCEMENT
               ├── REJECT
               ├── HOLD
               ├── ESCALATE
               ├── REVALIDATE
               └── COMMIT
                      ↓
                 EXECUTION
                      ↓
                 OUTCOME
                      ↓
                 PROVENANCE
```

---

# 68. Minimal Service Responsibilities for Future Code

The future reference implementation may use components such as:

```text
Action Service
Governance / Admissibility Engine
Binding Service
Continuity Evaluator
Enforcement Point
Protected Payments Executor
Route Registry
Provenance Store
```

These names are implementation suggestions, not architecture requirements.

---

# 69. Suggested Future Code Modules

A possible future structure:

```text
src/
├── models/
│   ├── action.py
│   ├── governance.py
│   ├── authorization.py
│   ├── continuity.py
│   ├── enforcement.py
│   └── provenance.py
│
├── governance/
│   ├── admissibility.py
│   ├── binding.py
│   ├── continuity.py
│   └── route_closure.py
│
├── enforcement/
│   ├── verifier.py
│   └── executor.py
│
├── provenance/
│   └── recorder.py
│
└── examples/
    └── bank_transfer.py
```

This structure is intentionally provisional.

The current document defines behavior before code organization is frozen.

---

# 70. Suggested Future Test Structure

```text
tests/
├── invariants/
│   ├── test_c01_no_direct_consequence.py
│   ├── test_c02_no_inherited_admissibility.py
│   ├── test_c03_exact_action_binding.py
│   ├── test_c04_executor_binding.py
│   ├── test_c05_authorization_lifetime.py
│   ├── test_c06_single_use.py
│   ├── test_c07_governance_continuity.py
│   ├── test_c08_commit_time_revalidation.py
│   ├── test_c09_enforcement_required.py
│   ├── test_c10_fail_closed.py
│   ├── test_c11_route_closure.py
│   ├── test_c12_no_alternate_route.py
│   ├── test_c13_provenance_completeness.py
│   ├── test_c14_decision_execution_linkage.py
│   └── test_c15_provenance_integrity.py
│
└── examples/
    └── test_bank_transfer.py
```

Again, this is a recommended future implementation shape, not part of the frozen architecture.

---

# 71. Minimum Happy-Path Tests

The implementation should eventually prove at least:

```text
test_valid_bank_transfer_completes_full_governance_chain()

test_transfer_requires_action_contract()

test_allow_does_not_execute_directly()

test_bound_authorization_is_required()

test_continuity_valid_required_before_enforcement()

test_commit_requires_enforcement()

test_route_closure_required_for_declared_system_state()

test_successful_transfer_emits_complete_provenance()
```

---

# 72. Minimum Failure Tests

```text
test_direct_agent_payment_blocked()

test_modified_amount_invalidates_prior_allow()

test_changed_beneficiary_rejected()

test_wrong_executor_rejected()

test_expired_authorization_rejected()

test_replayed_authorization_rejected()

test_revoked_authority_blocks_commit()

test_revoked_approval_blocks_commit()

test_stale_evidence_blocks_inherited_permission()

test_frozen_account_blocks_commit()

test_missing_continuity_fails_closed()

test_invalid_signature_fails_closed()

test_open_legacy_route_breaks_route_closure()

test_missing_execution_receipt_breaks_provenance_completeness()

test_broken_decision_link_detected()

test_provenance_tampering_detected()
```

---

# 73. What This Reference Scenario Proves When Implemented

If the future code and tests satisfy this specification, the reference implementation can demonstrate that, within its declared boundary:

1. the AI agent cannot directly create the protected financial consequence;
2. the action must be explicitly represented;
3. current governance state determines admissibility;
4. `ALLOW` is not execution permission;
5. authorization binds one exact action;
6. executor identity matters;
7. authorization validity is time-bounded;
8. replay is controlled;
9. governance state must remain current;
10. commit-time legitimacy is rechecked;
11. protected mutation depends on enforcement;
12. uncertainty fails closed;
13. alternate identified routes affect system-level governance;
14. successful execution leaves reconstructable provenance;
15. material provenance tampering is detectable.

---

# 74. What This Reference Scenario Does Not Prove

Even a successful implementation does not prove:

- universal banking security;
- legal or regulatory compliance;
- safety of every AI agent;
- correctness of every invoice;
- legitimacy of every upstream authority source;
- correctness of every enterprise policy;
- complete discovery of every infrastructure path;
- absence of software vulnerabilities;
- absence of privileged administrator compromise;
- correctness of the underlying banking rails;
- or universal route closure outside the declared system boundary.

The scenario demonstrates the architecture's control properties.

It does not make claims beyond them.

---

# 75. End-to-End Reference Summary

The complete use case can be summarized as:

```text
Treasury-Agent-01
      ↓
ACT-2041
Transfer ₹250,000
Corporate-Account-01 → Vendor-ABC
      ↓
Runtime Admissibility
      ↓
DEC-1882 = ALLOW
      ↓
Binding
      ↓
AUTHZ-7F92
      ↓
Continuity
      ↓
CONT-441 = VALID
      ↓
Enforcement
      ↓
ENF-992 = COMMIT
      ↓
Route Closure
      ↓
R1 = CLOSED
      ↓
Execution
      ↓
EXEC-881 = SUCCESS
      ↓
Outcome
      ↓
OUT-557 = PAYMENT_ACCEPTED
      ↓
Decision Provenance
      ↓
PROV-6601
```

The central architectural statement remains:

> **A consequential AI action should become real only when the enterprise can establish that the action is admissible, precisely bound, still current, structurally enforced, non-bypassable within the declared system boundary, and reconstructable afterward.**

---

# 76. Reference Scenario Status

```text
Reference Architecture v0.1       COMPLETE
Architectural Invariants C1–C15   COMPLETE
Action Contract                   COMPLETE
Execution Authorization           COMPLETE
Continuity Contract               COMPLETE
Enforcement Contract              COMPLETE
Provenance Contract               COMPLETE
Bank-Transfer Specification       COMPLETE
LinkedIn Capstone                 NEXT
Executable Implementation         PLANNED
Invariant Tests                   PLANNED
```

This document is the canonical end-to-end reference scenario for **The Agentic AI Governance Control Plane v0.1**.

---

## Related Specifications

- [Architectural Invariants](../../specifications/invariants.md)
- [Action Contract](../../specifications/action-contract.md)
- [Execution Authorization](../../specifications/execution-authorization.md)
- [Continuity Contract](../../specifications/continuity-contract.md)
- [Enforcement Contract](../../specifications/enforcement-contract.md)
- [Provenance Contract](../../specifications/provenance-contract.md)

## Related Architecture

- [Reference Architecture Index](../../docs/README.md)
- [Section 1 — Why This Capstone Exists](../../docs/01-why-this-capstone-exists.md)
- [Section 2 — From Model Risk to Consequence Risk](../../docs/02-from-model-risk-to-consequence-risk.md)
- [Section 3 — Runtime Admissibility](../../docs/03-runtime-admissibility.md)
- [Section 4 — Binding](../../docs/04-binding.md)
- [Section 5 — Continuity](../../docs/05-continuity.md)
- [Section 6 — Enforcement](../../docs/06-enforcement.md)
- [Section 7 — Route Closure](../../docs/07-route-closure.md)
- [Section 8 — Decision Provenance](../../docs/08-decision-provenance.md)

**Return to Main Repository:** [The Agentic AI Governance Control Plane](../../README.md)
