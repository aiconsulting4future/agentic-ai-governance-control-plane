# Provenance Contract

## The Agentic AI Governance Control Plane

**Version:** v0.1  
**Status:** Complete Specification  
**Specification Type:** Decision Provenance and Accountability Contract

---

## 1. Purpose

This specification defines how the governance lifecycle of a consequence-bearing AI action is preserved so that the legitimacy of the resulting consequence can later be reconstructed.

The Provenance Contract answers:

> **Can the enterprise reconstruct what was proposed, why it was allowed, under which governance state, through which execution path, and what consequence actually followed?**

The governing principle is:

> **Logging records activity. Provenance reconstructs legitimacy.**

Within the reference architecture, Decision Provenance is the final accountability layer:

```text
Proposed Action
      ↓
Admissibility
      ↓
Binding
      ↓
Continuity
      ↓
Enforcement
      ↓
Route Closure
      ↓
Execution
      ↓
DECISION PROVENANCE
```

The provenance contract does not replace governance.

It preserves the evidence that governance occurred and connects that evidence to the actual execution and outcome.

---

## 2. Architectural Position

The Provenance Contract spans the complete architecture.

It does not begin only after execution.

Each earlier stage emits facts or stable references that contribute to the final provenance chain.

Conceptually:

```text
Action
   ↓
Decision
   ↓
Authorization
   ↓
Continuity
   ↓
Enforcement
   ↓
Route
   ↓
Execution
   ↓
Outcome
```

The contract therefore consumes artifacts from:

- the Action Contract;
- Runtime Admissibility;
- Execution Authorization;
- the Continuity Contract;
- the Enforcement Contract;
- Route Closure;
- Execution;
- downstream outcome state.

---

# 3. Provenance Is Not Chain-of-Thought

Decision provenance does **not** require storing or exposing model chain-of-thought.

The provenance architecture preserves externally meaningful governance facts.

Conceptually:

```text
Action Proposed
      ↓
Governance State Evaluated
      ↓
Decision Produced
      ↓
Authorization Bound
      ↓
Continuity Validated
      ↓
Enforcement Applied
      ↓
Execution Performed
      ↓
Outcome Recorded
```

Therefore:

```text
Decision Provenance
    ≠
Model Chain-of-Thought
```

### Requirement

The Provenance Contract **MUST NOT** depend on private reasoning traces in order to establish execution legitimacy.

It should preserve:

```text
governance facts
state references
decision effects
reason codes
authorization facts
continuity results
enforcement results
route identity
execution evidence
outcome evidence
```

---

# 4. Decision-to-Outcome Chain

A consequence should be traceable across stable identifiers.

Canonical linkage:

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

Reference example:

```text
ACT-2041
    ↓
DEC-1882
    ↓
AUTHZ-7F92
    ↓
CONT-441
    ↓
ENF-992
    ↓
EXEC-881
    ↓
OUT-557
```

### Requirement

The purpose is not merely to store identifiers.

The relationship between them **MUST** remain reconstructable.

---

# 5. Provenance Record

A conceptual record may be represented as:

```text
DecisionProvenance = {
    action,
    actor,
    governance_state,
    decision,
    authorization,
    continuity,
    enforcement,
    route,
    execution,
    outcome
}
```

A possible structured representation is:

```json
{
  "schema_version": "0.1",
  "provenance_id": "PROV-6601",
  "action": {},
  "actor": {},
  "governance_state": {},
  "decision": {},
  "authorization": {},
  "continuity": {},
  "enforcement": {},
  "route": {},
  "execution": {},
  "outcome": {},
  "integrity": {}
}
```

The exact serialization is implementation-specific.

The architectural requirement is that the legitimacy chain remain reconstructable.

---

# 6. Action Provenance

At minimum, the provenance record should preserve or resolve:

```text
action_id
action_hash
action_type
material parameters
schema version
```

Example:

```json
{
  "action_id": "ACT-2041",
  "action_hash": "sha256:...",
  "action_type": "transfer_funds"
}
```

### Requirement

The provenance record **MUST** identify the same material action that was governed, bound, and executed.

The action recorded after execution must not be silently substituted with a different material action representation.

---

# 7. Actor Provenance

The originating actor should be traceable through fields such as:

```text
actor_id
workload / agent identity
tenant
environment
```

Example:

```json
{
  "actor_id": "Treasury-Agent-01",
  "actor_type": "ai_agent",
  "tenant": "Enterprise-A",
  "environment": "production"
}
```

### Requirement

The provenance record should preserve the identity context necessary to answer:

> **Who or what originated this governed action?**

---

# 8. Governance-State Provenance

The governance state used at decision time must remain reconstructable.

Relevant references may include:

```text
capability_snapshot_id
identity_snapshot_id
authority_snapshot_id
evidence_snapshot_id
scope_snapshot_id
policy_id
policy_version
risk_classification
approval_id
```

Example:

```json
{
  "capability_snapshot_id": "CAP-491",
  "identity_snapshot_id": "ID-772",
  "authority_snapshot_id": "AUTH-9841",
  "evidence_snapshot_id": "EVD-225",
  "scope_snapshot_id": "SCP-773",
  "policy_id": "TREASURY-PAYMENTS",
  "policy_version": 17,
  "risk_classification": "HIGH",
  "approval_id": "APR-118"
}
```

### Requirement

Historical governance state **MUST** remain distinguishable from current governance state.

A later audit must not evaluate historical legitimacy using only whatever state exists at audit time.

---

# 9. Decision Provenance

The admissibility decision should preserve:

```text
decision_id
decision_effect
decision_timestamp
reason_codes
```

Example:

```json
{
  "decision_id": "DEC-1882",
  "decision_effect": "ALLOW",
  "decision_timestamp": "2026-08-24T08:00:00Z",
  "reason_codes": [
    "AUTHORITY_CURRENT",
    "EVIDENCE_SUFFICIENT",
    "APPROVAL_PRESENT"
  ]
}
```

### Requirement

The provenance record should answer:

> **What governance decision was produced, when, and on what structured basis?**

---

# 10. Authorization Provenance

The bounded execution authorization should remain traceable through:

```text
authorization_id
bound_action_hash
bound_resource
bound_executor
issued_at
expires_at
usage / replay semantics
integrity reference
```

Example:

```json
{
  "authorization_id": "AUTHZ-7F92",
  "bound_action_hash": "sha256:...",
  "bound_resource": "Corporate-Account-01",
  "bound_executor": "Payments-Service-Prod",
  "issued_at": "2026-08-24T08:00:00Z",
  "expires_at": "2026-08-24T08:05:00Z"
}
```

### Requirement

The authorization must be traceable to:

```text
decision_id
```

and later:

```text
execution_id
```

so a reviewer can establish exactly which authorization controlled the execution.

---

# 11. Continuity Provenance

The provenance chain should preserve whether the bounded authorization remained legitimate at the consequence boundary.

At minimum:

```text
continuity_check_id
continuity_result
continuity_timestamp
```

Relevant state may include:

```text
authority
approval
policy
evidence
authorization lifetime
replay state
executor state
resource state
```

Example:

```json
{
  "continuity_check_id": "CONT-441",
  "continuity_result": "VALID",
  "continuity_timestamp": "2026-08-24T08:02:30Z",
  "dimensions": {
    "authority": "CURRENT",
    "approval": "PRESENT",
    "policy": "VALID",
    "evidence": "CURRENT",
    "authorization": "UNEXPIRED",
    "replay_state": "UNUSED"
  }
}
```

### Requirement

The provenance chain should be able to establish both:

```text
Valid when authorized
        +
Still valid when committed
```

---

# 12. Enforcement Provenance

The record should preserve which enforcement point admitted or rejected execution.

At minimum:

```text
enforcement_id
enforcement_point_id
enforcement_result
```

Example:

```json
{
  "enforcement_id": "ENF-992",
  "enforcement_point_id": "PAYMENT-GATEWAY-01",
  "enforcement_result": "COMMIT"
}
```

Relevant verification outcomes may include:

```text
signature
action hash
resource
executor
continuity
replay state
authorization lifetime
```

### Requirement

The provenance record should provide evidence that governance was actually applied at the consequence boundary.

---

# 13. Route Provenance

Because Route Closure is part of the architecture, the execution path should be identifiable.

Example:

```text
route_id = R1
```

where:

```text
R1 =
Treasury-Agent-01
→ Governance Control Plane
→ Payments-Service-Prod
→ Corporate-Account-01
```

The provenance record may also include:

```text
route_closure_state
```

Example:

```text
CLOSED
```

### Requirement

The route identity should enable later distinction between:

```text
governed execution
```

and:

```text
execution through an alternate route
```

when both can reach the same protected resource.

---

# 14. Execution Provenance

After protected mutation, the execution record should preserve:

```text
execution_id
execution_timestamp
execution_result
resource_version_before
resource_version_after
```

A conceptual execution receipt may include:

```json
{
  "execution_id": "EXEC-881",
  "authorization_id": "AUTHZ-7F92",
  "enforcement_id": "ENF-992",
  "action_hash": "sha256:...",
  "executor_id": "Payments-Service-Prod",
  "resource_id": "Corporate-Account-01",
  "commit_timestamp": "2026-08-24T08:02:32Z",
  "execution_result": "SUCCESS",
  "resource_version_before": 884,
  "resource_version_after": 885,
  "authorization_consumed": true
}
```

### Requirement

The execution receipt should describe what actually occurred at the consequence boundary.

It must not merely repeat what the system intended to occur.

---

# 15. Outcome Provenance

Execution result and business outcome are not always identical.

Example:

```text
Execution Result:
PAYMENT_ACCEPTED
```

may later become:

```text
Business Outcome:
PAYMENT_SETTLED
```

or:

```text
PAYMENT_REVERSED
```

Therefore:

```text
Execution Result
    ≠
Final Outcome
```

where the domain has meaningful downstream state.

### Requirement

The provenance architecture should preserve this distinction whenever downstream outcome affects accountability.

---

# 16. Outcome Record

A conceptual outcome record may include:

```json
{
  "outcome_id": "OUT-557",
  "execution_id": "EXEC-881",
  "outcome_type": "PAYMENT_ACCEPTED",
  "outcome_timestamp": "2026-08-24T08:02:33Z"
}
```

For later state transitions, additional linked outcome records may exist:

```text
OUT-557 = PAYMENT_ACCEPTED
OUT-558 = PAYMENT_SETTLED
```

### Requirement

Later business-state transitions should not overwrite historical outcome state in a way that destroys lineage.

---

# 17. Logging vs Provenance

Logging answers:

> **What happened?**

Decision Provenance answers:

> **Why was this consequence legitimate under the governance state that existed when it happened?**

Example:

```text
LOG:
Payment executed successfully.
```

versus:

```text
PROVENANCE:
Action ACT-2041
was evaluated under governance state G-991,
allowed by decision DEC-1882,
bound as AUTHZ-7F92,
remained valid at CONT-441,
committed through ENF-992,
executed as EXEC-881,
and produced outcome OUT-557.
```

### Requirement

Operational logs may contribute to provenance.

They are not sufficient merely because they record system activity.

---

# 18. Historical State vs Current State

A provenance system must preserve state **as it existed when governance occurred**.

Example:

```text
Authority at execution:
CURRENT
```

may later become:

```text
Authority during audit:
REVOKED
```

The correct audit question is not:

```text
Is authority valid now?
```

It is:

```text
What authority state applied when the action was governed and executed?
```

### Requirement

The provenance model **MUST NOT** silently substitute current-state values for historical governance-state values.

---

# 19. Provenance Integrity

A provenance record is useful only if material fields cannot be silently rewritten afterward.

Possible mechanisms include:

```text
append-only storage
cryptographic hashing
digital signatures
hash chaining
immutable event storage
write-once storage
external timestamping
```

The architecture does not mandate one mechanism.

The requirement is:

> **Unauthorized modification of material provenance must be detectable.**

---

# 20. Tamper Evidence Is Not Truth

The architecture maintains:

```text
Tamper Evidence
    ≠
Substantive Truth
```

A cryptographically preserved record may establish that the record was not altered after creation.

It does not independently prove that:

- the authority source was correct;
- evidence was true;
- the policy was appropriate;
- the approval was legitimate;
- the actor was uncompromised.

### Requirement

The Provenance Contract must not overclaim what integrity mechanisms establish.

---

# 21. Evidence, Authority, and Provenance Are Distinct

The architecture keeps three concepts separate:

```text
Evidence
```

supports the governance decision.

```text
Authority
```

establishes entitlement.

```text
Provenance
```

records how governance artifacts contributed to consequence.

Therefore:

```text
Evidence ≠ Authority ≠ Provenance
```

### Requirement

A provenance record must not be treated as the underlying evidence or authority itself merely because it references them.

---

# 22. Provenance Completeness

A provenance chain is complete when the implementation can reconstruct the governance lineage required for the consequence class.

For the reference architecture, that lineage includes:

```text
Action
Actor
Governance State
Decision
Authorization
Continuity
Enforcement
Route
Execution
Outcome
```

### Requirement

Completeness concerns **required lineage**, not unlimited data collection.

---

# 23. Data Minimization

Decision Provenance should not become uncontrolled collection of AI activity.

The architecture favors:

```text
Preserve governance facts
Reference evidence where possible
Minimize sensitive payload duplication
Do not capture hidden reasoning
Apply access controls
Apply retention policy
```

The governing principle is:

> **Accountability does not require storing everything. It requires preserving the right evidence.**

### Requirement

The provenance implementation should preserve enough data to reconstruct legitimacy while minimizing unnecessary sensitive duplication.

---

# 24. Referential Provenance

Where practical, provenance should reference authoritative immutable or versioned artifacts instead of duplicating them.

For example:

```text
authority_snapshot_id
evidence_snapshot_id
policy_version
approval_id
```

rather than embedding complete sensitive source payloads.

### Requirement

A reference used for provenance must remain resolvable for the retention period required by the implementation.

If referenced data may disappear, the system must preserve enough material information to maintain reconstructability.

---

# 25. Provenance at Commit

For high-consequence actions, provenance generation should not be treated merely as optional post-processing.

Where technically practical, a strong implementation may couple:

```text
protected mutation
+
authorization consumption
+
execution receipt
```

within the same transaction or equivalent consistency boundary.

Conceptually:

```text
BEGIN

validate authorization
validate continuity
apply mutation
consume authorization
persist execution receipt

COMMIT
```

This reduces failure states such as:

```text
mutation succeeded
but authorization remains reusable
```

or:

```text
mutation succeeded
but execution evidence was lost
```

---

# 26. Provenance Consistency Boundary

The architecture does not require a relational database transaction in every environment.

Equivalent mechanisms may include:

```text
transactional database
durable outbox
write-ahead log
event journal
append-only ledger
atomic state + receipt service
idempotent execution protocol
```

### Requirement

The implementation should define what consistency guarantee exists between:

```text
mutation
authorization consumption
execution receipt
```

and make that guarantee explicit.

---

# 27. Decision Replayability

Where governance decisions are deterministic, preserved state should support later decision replay.

Conceptually:

```text
Replay(
    action,
    governance_snapshot,
    policy_version
)
    →
decision'
```

Then:

```text
decision'
    =
decision_original
```

should normally hold when:

```text
same governed inputs
+
same policy version
+
same decision rules
```

are used.

### Requirement

Replayability concerns governance-state reconstruction.

It does not require reproducing hidden model reasoning.

---

# 28. Replayability Purpose

Replay allows an enterprise to distinguish:

```text
Decision changed because governance logic changed
```

from:

```text
Decision changed because underlying state changed
```

or:

```text
Decision changed because preserved inputs are incomplete
```

This is useful for:

- audit;
- regression testing;
- policy change analysis;
- incident investigation;
- architecture validation.

---

# 29. Structured Reason Codes

Governance artifacts should preserve machine-readable reason codes where practical.

Examples:

```text
AUTHORITY_REVOKED
EVIDENCE_STALE
APPROVAL_MISSING
ACTION_MISMATCH
EXECUTOR_MISMATCH
AUTHORIZATION_EXPIRED
REPLAY_DETECTED
ROUTE_OPEN
```

### Requirement

Human-readable explanations may be generated from structured facts.

They should not replace the machine-readable governance basis.

---

# 30. Reason-Code Lineage

Reason codes may exist at multiple stages.

Example:

```text
Admissibility:
RISK_HIGH

Continuity:
AUTHORITY_CURRENT

Enforcement:
ACTION_MATCH

Route Closure:
ROUTE_CLOSED
```

### Requirement

The provenance model should retain the stage that produced each reason code.

A reason code should not lose its semantic origin when incorporated into a combined provenance record.

---

# 31. Provenance Record Identity

Each final provenance record should have a stable identity.

Example:

```text
provenance_id = PROV-6601
```

### Requirement

A provenance identifier should identify one reconstructable governance lineage or lineage version.

The same `provenance_id` must not silently refer to materially different provenance content.

---

# 32. Provenance Schema Version

Example:

```text
schema_version = 0.1
```

### Requirement

The provenance schema must be versioned.

A material interpretation change must not occur silently because audit meaning depends on stable field semantics.

---

# 33. Canonical Provenance Representation

Where provenance integrity depends on hashing or signing, the material provenance representation should be canonicalized.

Conceptually:

```text
CanonicalProvenance =
    Canonicalize(MaterialProvenanceFields)
```

Then:

```text
ProvenanceHash =
    H(CanonicalProvenance)
```

Canonicalization should define:

```text
field ordering
identifier representation
timestamp representation
null semantics
array ordering
reason-code ordering
reference semantics
schema version
```

---

# 34. Provenance Integrity Object

A conceptual integrity block may be:

```json
{
  "algorithm": "implementation-defined",
  "record_hash": "sha256:...",
  "signature": "...",
  "previous_record_hash": "sha256:..."
}
```

The exact mechanism is implementation-specific.

### Requirement

Whatever mechanism is used must make unauthorized material modification detectable.

---

# 35. Append-Only Semantics

For high-consequence governance records, append-only semantics are preferable to destructive updates.

Instead of:

```text
record v1
    ↓ overwrite
record v2
```

prefer:

```text
record v1
    ↓
amendment / superseding event
    ↓
record v2
```

where historical state remains reconstructable.

### Requirement

Corrections should not erase the fact that an earlier record existed when that fact is material to accountability.

---

# 36. Corrections and Amendments

An implementation may need to correct erroneous metadata.

A safe conceptual pattern is:

```text
Original Record
      ↓
Correction Record
      ↓
Corrected Current View
```

### Requirement

Material amendments should preserve:

```text
what changed
who or what changed it
when
why
previous value
new value
```

where appropriate.

---

# 37. Access Control

Provenance may contain sensitive governance information.

Access control should consider:

```text
actor identity
authority information
approval identity
risk classification
resource identifiers
policy decisions
security events
business outcomes
```

### Requirement

The fact that provenance supports accountability does not imply unrestricted visibility.

Access should follow enterprise policy.

---

# 38. Retention

Retention may vary by:

```text
consequence class
regulatory requirement
enterprise policy
contractual obligation
incident requirements
business need
```

### Requirement

Retention policy should be explicit enough that required lineage does not disappear before its accountability obligation expires.

---

# 39. Deletion and Legal Requirements

Where deletion requirements apply, implementations may need to balance:

```text
privacy / deletion obligations
```

with:

```text
audit / accountability obligations
```

This specification does not prescribe jurisdiction-specific retention law.

### Requirement

The implementation should define how provenance integrity and reconstructability are maintained under its applicable retention and deletion policy.

---

# 40. Distributed Provenance

Governance artifacts may exist across multiple systems.

For example:

```text
Action Service
Decision Service
Authorization Service
Continuity Service
Enforcement Service
Execution Service
Business Outcome System
```

### Requirement

A single database is not required.

Stable identifiers and resolvable relationships are required.

---

# 41. Provenance Correlation

Correlation should rely on stable semantic identifiers rather than timestamps alone.

Preferred:

```text
ACT-2041
DEC-1882
AUTHZ-7F92
CONT-441
ENF-992
EXEC-881
OUT-557
```

Not merely:

```text
events occurring around 08:02
```

### Requirement

Temporal proximity may assist investigation but must not be the only mechanism establishing lineage.

---

# 42. Clock and Timestamp Semantics

Provenance records use timestamps such as:

```text
decision_timestamp
issued_at
continuity_timestamp
commit_timestamp
outcome_timestamp
```

### Requirement

The implementation should define:

- authoritative time source;
- timezone representation;
- ordering semantics;
- clock-skew handling where distributed services are involved.

Preferred representation is a consistent UTC timestamp format.

---

# 43. Event Ordering

Distributed systems may emit events asynchronously.

Timestamp order may not always equal causal order.

### Requirement

Where causal reconstruction matters, stable linkage and sequence semantics should supplement timestamps.

Possible mechanisms include:

```text
parent identifiers
sequence numbers
event versions
causal references
transaction identifiers
```

---

# 44. Provenance and Route Closure

Route Closure defines whether identified consequence-bearing routes are governed.

Provenance records **which route was actually used**.

Therefore:

```text
Route Closure
    ≠
Route Provenance
```

Route Closure is a precondition / architecture property.

Route Provenance is evidence of the execution path actually taken.

### Requirement

The provenance record must not infer system-wide route closure merely because the executed route was governed.

---

# 45. Provenance and Enforcement

The Enforcement Contract emits execution-layer evidence.

The Provenance Contract links that evidence to the full governance lineage.

Therefore:

```text
Execution Receipt
    ⊂
Decision Provenance
```

The execution receipt is part of provenance.

It is not the complete provenance record.

---

# 46. Provenance and Continuity

The continuity record establishes current execution legitimacy.

The provenance record preserves:

```text
bound state
+
current continuity state
+
continuity outcome
```

### Requirement

Historical authorization must not be represented as though it alone proves commit-time validity.

---

# 47. Provenance and Binding

Binding establishes:

```text
what exact action
which resource
which executor
which governance-state basis
```

The Provenance Contract preserves that binding and links it to execution.

### Requirement

A reviewer should be able to determine whether the execution consumed the same bounded authorization produced from the decision.

---

# 48. Provenance and Runtime Admissibility

The provenance chain should preserve enough information to reconstruct:

```text
what governance state was evaluated
what decision was produced
why the decision had that effect
```

This supports later validation of:

```text
ALLOW
HOLD
ESCALATE
DENY
```

without relying on narrative explanations alone.

---

# 49. Bank-Transfer Reference Provenance

Reference action:

```text
Treasury-Agent-01
proposes:

₹250,000
Corporate-Account-01
→ Vendor-ABC
```

The governance lineage becomes:

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

The resulting provenance should allow a reviewer to answer:

```text
What action was proposed?
Which actor proposed it?
Which governance state applied?
Why was it allowed?
Which approval applied?
What exactly was authorized?
Was that authorization still valid?
Which enforcement point committed it?
Which route was used?
What executed?
What outcome followed?
```

---

# 50. Reference Bank-Transfer Provenance Record

A conceptual record is:

```json
{
  "schema_version": "0.1",
  "provenance_id": "PROV-6601",
  "action": {
    "action_id": "ACT-2041",
    "action_type": "transfer_funds",
    "action_hash": "sha256:...",
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
    "bound_action_hash": "sha256:...",
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

# 51. Invariant C13 — Provenance Completeness

For every successful protected mutation:

```text
ProtectedMutationSucceeded(a)
    =>
ReconstructableGovernanceChain(a)
```

Meaning:

> **A successful protected mutation must leave sufficient provenance to reconstruct the governance chain that authorized and executed it.**

### Contract obligation

The Provenance Contract **MUST** preserve enough material lineage for a successful protected consequence to be reconstructed.

---

# 52. Invariant C14 — Decision-to-Execution Linkage

For every protected execution:

```text
Execution
    =>
LinkedTo(
        Decision,
        Authorization,
        Continuity,
        Enforcement
    )
```

Meaning:

> **A protected execution must be traceably linked to the governance decision, bounded authorization, continuity result, and enforcement decision that permitted it.**

### Contract obligation

The provenance system **MUST** preserve stable linkage between:

```text
decision_id
authorization_id
continuity_check_id
enforcement_id
execution_id
```

---

# 53. Invariant C15 — Provenance Integrity

For material provenance:

```text
UnauthorizedModification(Provenance)
    =>
DetectableIntegrityFailure
```

Meaning:

> **Unauthorized modification of material provenance records must be detectable.**

### Contract obligation

Material provenance **MUST** be protected by a mechanism that makes unauthorized modification detectable.

---

# 54. Relationship to Earlier Invariants

The Provenance Contract provides evidence about prior control stages.

It does not re-execute those controls.

It can help establish evidence for:

```text
C1 — No Direct Consequence
C2 — No Inherited Admissibility
C3 — Exact Action Binding
C4 — Executor Binding
C5 — Bounded Authorization Lifetime
C6 — Single-Use Where Required
C7 — Governance Continuity
C8 — Commit-Time Revalidation
C9 — Enforcement Required
C10 — Fail Closed at Enforcement Boundary
C11 — Route Closure
C12 — No Alternate Consequence Path
```

but provenance alone does not prove those invariants were truly satisfied unless the underlying implementation evidence supports them.

---

# 55. Provenance Completeness Levels

Different consequence classes may require different provenance depth.

A practical implementation may define:

```text
BASIC
STANDARD
HIGH_CONSEQUENCE
```

For example:

### BASIC

```text
action
decision
execution
outcome
```

### STANDARD

```text
action
actor
decision
authorization
execution
outcome
```

### HIGH_CONSEQUENCE

```text
action
actor
governance_state
decision
authorization
continuity
enforcement
route
execution
outcome
integrity
```

### Requirement

Any tiering must still satisfy the architectural invariants applicable to the consequence class.

For protected high-consequence execution in this reference architecture, the full decision-to-execution chain is the expected baseline.

---

# 56. Incomplete Provenance

A provenance record should explicitly represent missing required lineage.

Examples:

```text
MISSING_DECISION_LINK
MISSING_AUTHORIZATION
MISSING_CONTINUITY_RECORD
MISSING_ENFORCEMENT_RECORD
MISSING_EXECUTION_RECEIPT
MISSING_ROUTE_ID
MISSING_OUTCOME_LINK
```

### Requirement

The system **MUST NOT** silently represent an incomplete governance chain as complete.

---

# 57. Provenance Validation

A conceptual validation function is:

```text
ValidateProvenance(P):

    require schema supported
    require action present
    require actor present
    require governance state resolvable
    require decision present
    require authorization linked
    require continuity linked
    require enforcement linked
    require route identified
    require execution linked
    require outcome linked where required
    require integrity valid

    return COMPLETE
```

Possible results:

```text
COMPLETE
INCOMPLETE
INTEGRITY_INVALID
LINKAGE_INVALID
REFERENCE_UNRESOLVED
SCHEMA_INVALID
```

---

# 58. Linkage Validation

Stable identifiers alone are insufficient if they are inconsistent.

Example:

```text
AUTHZ-7F92.action_id = ACT-2041
```

must agree with:

```text
EXEC-881.action_id = ACT-2041
```

and:

```text
ENF-992.authorization_id = AUTHZ-7F92
```

### Requirement

The provenance validator should verify material cross-record consistency.

---

# 59. Action-Hash Consistency

Where action hashes are used:

```text
DecisionActionHash
BoundActionHash
ExecutionActionHash
ReceiptActionHash
```

should be consistent with the same material action.

### Requirement

A hash mismatch must be treated as a provenance integrity or linkage failure, not silently ignored.

---

# 60. Resource Consistency

The protected resource should remain consistent across:

```text
Action
Authorization
Enforcement
Execution
```

unless an explicitly modeled transformation applies.

### Requirement

A provenance chain should not present:

```text
authorized resource A
```

and:

```text
executed resource B
```

as one legitimate lineage without explicit governance explaining the difference.

---

# 61. Executor Consistency

The bound executor and actual executor should be reconstructable.

Expected relation:

```text
BoundExecutor
    =
ExecutionExecutor
```

for direct executor-binding semantics.

A mismatch should be explicit.

---

# 62. Authorization Consumption Provenance

Where single-use authorization applies, provenance should preserve:

```text
authorization_consumed = true
```

and sufficient execution linkage to establish which execution consumed it.

### Requirement

The same single-use authorization should not appear as successfully consumed by multiple protected executions without an explicit integrity failure or incident state.

---

# 63. Route-Closure State Provenance

The provenance record may preserve the route-closure state that applied to the protected resource at execution time.

Example:

```text
route_closure_state = CLOSED
```

### Requirement

Historical route-closure state should not be reconstructed solely from current system configuration if route topology has changed since execution.

---

# 64. Policy-Version Provenance

The policy version used by the decision should be preserved.

Example:

```text
policy_id      = TREASURY-PAYMENTS
policy_version = 17
```

### Requirement

Replay or audit should use the historical policy version when evaluating the historical governance decision.

---

# 65. Approval Provenance

Where human approval was required, the provenance chain should preserve:

```text
approval_id
approval scope
approval status at decision
approval status at continuity
approved action hash
```

as applicable.

### Requirement

The presence of an approval identifier does not by itself prove approval remained valid at commit.

That relationship is established through continuity provenance.

---

# 66. Evidence Provenance

Evidence records should be referenced in a way that permits a reviewer to establish:

```text
what evidence supported the decision
what evidence state was current at continuity
whether evidence materially changed
```

### Requirement

Evidence provenance should avoid unnecessary duplication of sensitive evidence payloads where stable references are sufficient.

---

# 67. Authority Provenance

Authority provenance should allow reconstruction of:

```text
authority state at decision
authority state at continuity
authority reference used
material authority transition
```

where relevant.

### Requirement

A later authority revocation must not erase the fact that a different authority state may have applied historically.

---

# 68. Enforcement Failure Provenance

Protected actions that do not execute may still require provenance.

For example:

```text
Action ACT-2041
Decision DEC-1882 = ALLOW
Authorization AUTHZ-7F92
Continuity CONT-441 = VALID
Enforcement ENF-992 = REJECT
Reason = ACTION_MISMATCH
No execution
```

### Requirement

Where operationally useful, failed protected execution attempts should preserve enough lineage to explain why consequence did not occur.

---

# 69. Denied-Action Provenance

A denied action may never create:

```text
authorization
continuity
enforcement
execution
outcome
```

but the governance decision itself may still be preserved.

Example:

```text
ACT-2100
    ↓
DEC-1900 = DENY
```

### Requirement

The Provenance Contract may support partial governance lineage for non-executed actions.

C13 applies specifically to successful protected mutation.

---

# 70. Provenance for HOLD and ESCALATE

A `HOLD` or `ESCALATE` may create intermediate governance records.

Example:

```text
ACT-2200
    ↓
DEC-1950 = ESCALATE
    ↓
APR-130 = PENDING
```

If later resolved:

```text
APR-130 = APPROVED
    ↓
DEC-1951 = ALLOW
```

### Requirement

Intermediate decisions should not be overwritten when they are material to the eventual governance history.

---

# 71. Multi-Step Governance Lineage

An action may pass through more than one decision cycle because of revalidation.

Example:

```text
ACT-2041
    ↓
DEC-1882 = ALLOW
    ↓
AUTHZ-7F92
    ↓
CONT-441 = REVALIDATE
    ↓
DEC-1885 = ALLOW
    ↓
AUTHZ-7FA1
    ↓
CONT-446 = VALID
    ↓
ENF-998 = COMMIT
```

### Requirement

The provenance chain should preserve both the abandoned and final authorization path where they are material.

The final execution must link to the authorization that actually controlled it.

---

# 72. Provenance Graph

The provenance structure is naturally a directed graph rather than merely a flat log.

Conceptually:

```text
Action
 ├──→ Decision-1
 │       └──→ Authorization-1
 │                └──→ Continuity = REVALIDATE
 │
 └──→ Decision-2
         └──→ Authorization-2
                  └──→ Continuity = VALID
                           └──→ Enforcement
                                    └──→ Execution
                                             └──→ Outcome
```

### Requirement

The implementation may store this relationally, as events, documents, or graph structures.

The semantic relationships must remain reconstructable.

---

# 73. Provenance Event Model

An alternative implementation may store provenance as events:

```text
ACTION_PROPOSED
DECISION_PRODUCED
AUTHORIZATION_BOUND
CONTINUITY_EVALUATED
ENFORCEMENT_DECIDED
EXECUTION_COMMITTED
OUTCOME_RECORDED
```

### Requirement

Event-based storage must still satisfy C13–C15.

Event ordering and linkage must remain reconstructable.

---

# 74. Provenance Snapshot Model

A system may also maintain a materialized provenance view assembled from authoritative records.

Example:

```text
CurrentProvenanceView(PROV-6601)
```

### Requirement

The materialized view must remain traceable to the underlying records or events from which it was derived.

---

# 75. Provenance Query Obligations

A useful implementation should support questions such as:

```text
Given execution_id:
Which action caused it?

Given authorization_id:
Was it consumed, and by which execution?

Given action_id:
Which decisions were produced?

Given decision_id:
Which governance state applied?

Given outcome_id:
Which execution produced it?

Given resource_id:
Which governed actions mutated it?

Given actor_id:
Which protected actions originated from it?
```

These queries are implementation goals derived from reconstructability.

---

# 76. Reference Reconstruction Query

Given:

```text
execution_id = EXEC-881
```

the system should be able to resolve:

```text
EXEC-881
    → ENF-992
    → CONT-441
    → AUTHZ-7F92
    → DEC-1882
    → ACT-2041
```

and:

```text
EXEC-881
    → OUT-557
```

This is the core decision-to-outcome reconstruction path.

---

# 77. Provenance Verification Report

A future implementation may expose a verification result:

```json
{
  "provenance_id": "PROV-6601",
  "status": "VERIFIED",
  "completeness": "COMPLETE",
  "integrity": "VALID",
  "linkage": "VALID",
  "route_state": "CLOSED",
  "execution_result": "SUCCESS"
}
```

Possible statuses:

```text
VERIFIED
INCOMPLETE
INTEGRITY_FAILURE
LINKAGE_FAILURE
REFERENCE_FAILURE
UNRESOLVED
```

### Requirement

Such a report must not imply more than the underlying verification actually establishes.

---

# 78. Provenance Failure Codes

Illustrative reason codes include:

```text
PROVENANCE_SCHEMA_INVALID
ACTION_REFERENCE_MISSING
ACTOR_REFERENCE_MISSING
GOVERNANCE_STATE_UNRESOLVED
DECISION_REFERENCE_MISSING
AUTHORIZATION_REFERENCE_MISSING
CONTINUITY_REFERENCE_MISSING
ENFORCEMENT_REFERENCE_MISSING
ROUTE_REFERENCE_MISSING
EXECUTION_REFERENCE_MISSING
OUTCOME_REFERENCE_MISSING
ACTION_HASH_MISMATCH
RESOURCE_LINK_MISMATCH
EXECUTOR_LINK_MISMATCH
AUTHORIZATION_EXECUTION_LINK_MISMATCH
INTEGRITY_INVALID
REFERENCE_UNRESOLVED
RETENTION_GAP
EVENT_ORDER_UNRESOLVED
```

---

# 79. Test Intent

The future implementation should include tests such as:

```text
test_successful_mutation_has_complete_provenance()

test_execution_links_to_decision()

test_execution_links_to_authorization()

test_execution_links_to_continuity()

test_execution_links_to_enforcement()

test_execution_links_to_outcome()

test_route_id_is_preserved()

test_historical_policy_version_is_preserved()

test_historical_authority_state_is_preserved()

test_execution_receipt_matches_bound_action_hash()

test_execution_receipt_matches_bound_resource()

test_execution_receipt_matches_bound_executor()

test_single_use_authorization_links_to_one_successful_execution()

test_revalidation_preserves_prior_governance_branch()

test_denied_action_does_not_fake_execution_lineage()

test_missing_required_provenance_is_detected()

test_broken_identifier_linkage_is_detected()

test_provenance_tampering_is_detected()

test_provenance_replay_reconstructs_original_decision()

test_current_state_does_not_overwrite_historical_state()

test_execution_result_and_business_outcome_are_distinct()

test_sensitive_evidence_is_referenced_not_duplicated_where_configured()

test_provenance_record_schema_is_versioned()
```

These names are illustrative until the code structure is finalized.

---

# 80. Security Considerations

Provenance is security-sensitive because attackers may seek to:

```text
erase evidence
rewrite governance history
substitute authorization identifiers
alter action hashes
hide alternate routes
change approval history
change authority history
fabricate execution receipts
detach outcomes from executions
forge timestamps
truncate event chains
```

Implementations should consider:

- integrity protection;
- authorization to write provenance;
- authorization to amend provenance;
- append-only storage;
- cryptographic verification;
- trusted timestamps;
- backup and recovery;
- retention controls;
- access segmentation;
- audit of provenance administration.

---

# 81. Privacy Considerations

Provenance may contain personal or commercially sensitive information.

The contract should support:

```text
minimum necessary data
stable references
field-level access control
retention policy
pseudonymous identifiers where appropriate
separation of operational and audit views
```

### Requirement

Accountability should not become an excuse for indiscriminate data capture.

---

# 82. Reliability Considerations

A provenance implementation should consider failure modes such as:

```text
execution succeeds but receipt write fails
event bus unavailable
outcome system delayed
cross-service identifier lost
clock disagreement
duplicate events
reordered events
partial record replication
integrity service unavailable
```

### Requirement

The implementation should define recovery behavior for incomplete provenance.

---

# 83. Idempotency

Duplicate delivery of the same provenance event should not create false duplicate governance events.

Example:

```text
EXECUTION_RECEIPT EXEC-881
```

received twice should not become:

```text
EXEC-881
EXEC-882
```

unless a second execution truly occurred.

### Requirement

Event ingestion should preserve semantic identity.

---

# 84. Recovery

If an execution receipt is temporarily unavailable but can be reconstructed from an authoritative execution journal, the provenance layer may later repair the lineage.

### Requirement

Repair should be explicit and auditable.

The system should distinguish:

```text
record created at execution
```

from:

```text
record reconstructed later
```

where that distinction matters.

---

# 85. Provenance Trust Boundary

The provenance layer itself is not automatically trusted merely because it stores audit records.

A robust implementation should establish:

```text
who may emit each artifact
who may verify each artifact
who may link artifacts
who may amend records
who may administer retention
```

### Requirement

Issuer identity and integrity context should be preserved where needed.

---

# 86. Provenance Issuer Identity

Different records may originate from:

```text
governance decision service
binding service
continuity service
enforcement point
execution service
outcome system
```

A record may include:

```text
issuer_id
```

or equivalent provenance-source identity.

### Requirement

Where source authenticity is material, provenance should be able to establish which trusted component emitted the record.

---

# 87. Provenance Without Centralization

The architecture does not require a central immutable ledger.

A valid implementation may use distributed authoritative records if:

```text
relationships are stable
integrity is verifiable
historical state is preserved
required references remain resolvable
```

The contract is semantic, not vendor-specific.

---

# 88. What Provenance Proves

Decision provenance can establish that:

- a specific action was proposed;
- a specific actor originated it;
- a defined governance state was evaluated;
- a specific decision was produced;
- a specific authorization was bound;
- continuity was evaluated before consequence;
- a specific enforcement point admitted execution;
- a specific route was used;
- a specific executor performed the mutation;
- an execution receipt exists;
- an outcome is linked to execution;
- the chain can later be reconstructed;
- material provenance modification is detectable when integrity verification succeeds.

---

# 89. What Provenance Does Not Prove

Decision provenance does **not** itself prove that:

- every source fact was true;
- every authority source was legitimate;
- every policy was correct;
- every approval was appropriate;
- every implementation component was uncompromised;
- every route outside the declared architecture boundary was governed;
- every outcome was desirable;
- the model's private reasoning was correct.

Provenance preserves the history of governance.

It does not replace governance.

---

# 90. Relationship to the Eight-Document Reference Architecture

This contract is derived from the complete v0.1 architecture.

## Section 1 — Why This Capstone Exists

Establishes the overall governance control chain and the accountability plane.

The Provenance Contract implements the accountability obligation at the end of that chain.

## Section 2 — From Model Risk to Consequence Risk

Defines the consequence boundary and the need to govern protected mutation.

Provenance records the governance lineage associated with that consequence.

## Section 3 — Runtime Admissibility

Defines the governance state and the decision effects:

```text
ALLOW
HOLD
ESCALATE
DENY
```

The Provenance Contract preserves the decision and its governed state.

## Section 4 — Binding

Defines the bounded authorization and stable references to action, resource, executor, governance state, policy, approval, lifetime, replay state, and integrity.

The Provenance Contract preserves those binding facts and links them to execution.

## Section 5 — Continuity

Defines whether authorization remains legitimate at execution time.

The Provenance Contract preserves:

```text
continuity_check_id
continuity result
commit-time state
```

## Section 6 — Enforcement

Defines how governance becomes technically controlling and produces enforcement and execution evidence.

The Provenance Contract preserves:

```text
enforcement_id
enforcement_point_id
enforcement_result
execution receipt
```

## Section 7 — Route Closure

Defines whether alternate consequence-bearing routes are governed.

The Provenance Contract preserves:

```text
route_id
route_closure_state
```

for the execution path.

## Section 8 — Decision Provenance

Defines the final accountability obligation:

> **Can the legitimacy and outcome of the consequence be reconstructed?**

This contract is the implementation-facing specification of that section.

---

# 91. Specification Boundary

This specification defines the accountability contract for the complete governance chain.

It does not define new architecture beyond the eight-document v0.1 reference architecture.

It does not introduce additional architectural invariants beyond:

```text
C13 — Provenance Completeness
C14 — Decision-to-Execution Linkage
C15 — Provenance Integrity
```

With this contract complete, the formal contract layer is ready to support the end-to-end reference scenario.

The next planned artifact is:

```text
examples/bank-transfer/README.md
```

---

# 92. Specification Status

```text
Reference Architecture v0.1       COMPLETE
Architectural Invariants C1–C15   COMPLETE
Action Contract v0.1              COMPLETE
Execution Authorization v0.1      COMPLETE
Continuity Contract v0.1          COMPLETE
Enforcement Contract v0.1         COMPLETE
Provenance Contract v0.1          COMPLETE
Bank-Transfer Specification       NEXT
LinkedIn Capstone                 PLANNED
Executable Implementation         PLANNED
Invariant Tests                   PLANNED
```

This document is the canonical provenance specification for **The Agentic AI Governance Control Plane v0.1**.

---

## Related Specifications

- [Architectural Invariants](invariants.md)
- [Action Contract](action-contract.md)
- [Execution Authorization](execution-authorization.md)
- [Continuity Contract](continuity-contract.md)
- [Enforcement Contract](enforcement-contract.md)

## Related Architecture

- [Reference Architecture Index](../docs/README.MD)
- [Section 1 — Why This Capstone Exists](../docs/01-why-this-capstone-exists.md)
- [Section 2 — From Model Risk to Consequence Risk](../docs/02-from-model-risk-to-consequence-risk.md)
- [Section 3 — Runtime Admissibility](../docs/03-runtime-admissibility.md)
- [Section 4 — Binding](../docs/04-binding.md)
- [Section 5 — Continuity](../docs/05-continuity.md)
- [Section 6 — Enforcement](../docs/06-enforcement.md)
- [Section 7 — Route Closure](../docs/07-route-closure.md)
- [Section 8 — Decision Provenance](../docs/08-decision-provenance.md)

**Return to Main Repository:** [The Agentic AI Governance Control Plane](../README.MD)
