# 8. Decision Provenance: Can the Legitimacy of the Consequence Be Reconstructed?

The previous sections establish whether an action is admissible, exactly what is authorized, whether that authorization remains current, whether execution is structurally enforced, and whether alternate consequence-bearing routes are closed.

The final architectural obligation is accountability.

After a consequential action occurs, the enterprise should be able to answer:

> **What was proposed, why was it allowed, under which authority and evidence, through which execution path, and what consequence actually followed?**

That is the purpose of decision provenance.

The governing principle is:

> **Logging records activity. Provenance reconstructs legitimacy.**

---

## 8.1 The Provenance Problem

A conventional operational log may record:

```text
09:03:12
POST /payments
200 OK

```text
Treasury-Agent-01
transferred ₹250,000
to Vendor-ABC
```

Neither necessarily establishes whether the action was legitimate when it occurred.

For a consequence-bearing AI system, accountability may require reconstructing:

* the proposed action;
* the originating actor;
* the governance state evaluated;
* the admissibility decision;
* the authority relied upon;
* the evidence relied upon;
* the policy version applied;
* the human approval, if required;
* the bounded execution authorization;
* the continuity result at commit time;
* the enforcement point;
* the execution route;
* the actual mutation;
* and the resulting outcome.

Decision provenance preserves the relationships among those artifacts.

---

## 8.2 Provenance Is Not Chain-of-Thought

Decision provenance does not require storing or exposing private model reasoning.

The relevant artifacts are externally meaningful governance facts:

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

The architecture is concerned with reconstructing legitimacy, not reconstructing hidden reasoning traces.

---

## 8.3 The Decision-to-Outcome Chain

A consequence should be traceable across stable identifiers.

Conceptually:

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

For example:

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

The purpose is not merely to store identifiers.

The relationship between them must remain reconstructable.

---

## 8.4 What the Provenance Record Must Preserve

A conceptual provenance record may contain:

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

At minimum, a protected execution should be traceable to:

### Action

```text
action_id
action_hash
action_type
material parameters
```

### Actor

```text
actor_id
workload / agent identity
tenant
environment
```

### Governance State

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

### Decision

```text
decision_id
decision_effect
decision_timestamp
reason_codes
```

### Authorization

```text
authorization_id
bound_action_hash
bound_resource
bound_executor
issued_at
expires_at
```

### Continuity

```text
continuity_check_id
continuity_result
continuity_timestamp
```

### Enforcement

```text
enforcement_id
enforcement_point_id
enforcement_result
```

### Route

```text
route_id
route_closure_state
```

### Execution

```text
execution_id
execution_timestamp
execution_result
resource_version_before
resource_version_after
```

### Outcome

```text
outcome_id
outcome_type
outcome_timestamp
```

The exact schema is implementation-specific.

The architectural requirement is that the legitimacy chain remains reconstructable.

---

## 8.5 Logging vs Provenance

The distinction can be stated simply.

Logging answers:

> **What happened?**

Decision provenance answers:

> **Why was this consequence legitimate under the governance state that existed when it happened?**

For example:

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

The second record establishes lineage.

---

## 8.6 Governance-State Provenance

The provenance record should preserve the governance state used at decision time.

For example:

```text
authority_snapshot_id = AUTH-9841
evidence_snapshot_id  = EVD-225
policy_version        = 17
approval_id           = APR-118
```

This matters because the current state may later be different.

For example:

```text
Authority at execution:
CURRENT
```

may later become:

```text
Authority during audit:
REVOKED
```

A later audit must not incorrectly evaluate historical legitimacy using only current state.

Provenance should preserve:

```text
state at decision
```

and, where relevant:

```text
state at continuity check
```

---

## 8.7 Binding and Execution Provenance

The authorization should be traceable to the execution that consumed it.

Conceptually:

```text
DEC-1882
    ↓
AUTHZ-7F92
    ↓
EXEC-881
```

This enables a reviewer to establish:

* which decision authorized the action;
* exactly what action was bound;
* which executor was authorized;
* which resource was protected;
* whether the authorization was still valid;
* whether it was used once;
* and which execution resulted.

Without this linkage, execution may be observable without being attributable to a specific governance decision.

---

## 8.8 Continuity Provenance

A valid binding at `t0` is not sufficient.

The provenance chain should preserve whether the authorization remained valid at the consequence boundary.

For example:

```text
continuity_check_id = CONT-441
continuity_result   = VALID
```

with relevant current-state results such as:

```text
authority     = CURRENT
approval      = PRESENT
policy        = VALID
evidence      = CURRENT
authorization = UNEXPIRED
replay_state  = UNUSED
```

This establishes:

```text
Valid when authorized
        +
Still valid when committed
```

rather than relying only on historical approval.

---

## 8.9 Enforcement Provenance

The record should preserve which enforcement point admitted execution.

For example:

```text
enforcement_point_id = PAYMENT-GATEWAY-01
enforcement_result   = COMMIT
```

Relevant verification outcomes may include:

```text
signature      = VALID
action_hash    = MATCH
resource       = MATCH
executor       = MATCH
continuity     = VALID
replay_state   = UNUSED
```

This provides evidence that governance was actually applied at the consequence boundary.

---

## 8.10 Route Provenance

Because route closure is part of the architecture, the execution path should also be identifiable.

For example:

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

This helps distinguish a valid governed execution from an execution reaching the same protected resource through an alternate route.

---

## 8.11 Execution Receipt

After a successful protected mutation, the enforcement layer should produce an execution receipt.

A conceptual receipt may contain:

```text
execution_id
authorization_id
action_hash
executor_id
resource_id
commit_timestamp
execution_result
resource_version_before
resource_version_after
authorization_consumed
```

The receipt provides evidence of what occurred at the consequence boundary.

It should be linked to the broader decision provenance record.

---

## 8.12 Execution Result and Business Outcome

Execution and final business outcome are not always identical.

For example:

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

where the enterprise process has meaningful downstream state.

The provenance architecture should preserve that distinction where necessary.

---

## 8.13 Provenance Integrity

A provenance record is useful only if material fields cannot be silently rewritten afterward.

Possible mechanisms include:

* append-only storage;
* cryptographic hashing;
* digital signatures;
* hash chaining;
* immutable event storage;
* write-once storage;
* external timestamping.

The architecture does not mandate a particular mechanism.

The requirement is:

> **Unauthorized modification of material provenance must be detectable.**

However:

```text
Tamper Evidence
    ≠
Substantive Truth
```

A cryptographically preserved record proves integrity of the stored record.

It does not independently prove that the underlying evidence or authority was correct.

---

## 8.14 Evidence, Authority, and Provenance Must Remain Distinct

Evidence, authority, and provenance serve different purposes.

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

records how those elements contributed to the consequence.

Therefore:

```text
Evidence ≠ Authority ≠ Provenance
```

This separation prevents audit artifacts from being mistaken for substantive governance proof.

---

## 8.15 Replayability

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

Replayability allows the enterprise to distinguish:

```text
Decision changed because governance logic changed
```

from:

```text
Decision changed because the underlying state changed
```

The objective is replay of the governance decision path, not reproduction of private model reasoning.

---

## 8.16 Structured Reason Codes

Governance decisions should preserve machine-readable reason codes where practical.

Examples include:

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

Structured reason codes improve:

* testing;
* audit;
* incident investigation;
* deterministic replay;
* policy analysis.

Human-readable explanations can be generated from these facts.

They should not replace them.

---

## 8.17 Data Minimization

Decision provenance should not become uncontrolled collection of AI activity.

The architecture should preserve the information required to establish legitimacy while avoiding unnecessary duplication of sensitive information.

A practical principle is:

```text
Preserve governance facts
Reference evidence where possible
Minimize sensitive payload duplication
Do not capture hidden reasoning
Apply access controls
Apply retention policy
```

Therefore:

> **Accountability does not require storing everything. It requires preserving the right evidence.**

---

## 8.18 Provenance at Commit

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

## 8.19 Bank Transfer Provenance Example

Consider:

```text
Treasury-Agent-01
proposes:

₹250,000
Corporate-Account-01
→ Vendor-ABC
```

The governance chain becomes:

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

The resulting provenance record allows a reviewer to establish:

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

That is the accountability obligation this architecture requires.

---

## 8.20 Invariant C13 — Provenance Completeness

For every successful protected mutation:

```text
ProtectedMutationSucceeded(a)
    =>
ReconstructableGovernanceChain(a)
```

Meaning:

> **A successful protected mutation must leave sufficient provenance to reconstruct the governance chain that authorized and executed it.**

---

## 8.21 Invariant C14 — Decision-to-Execution Linkage

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

---

## 8.22 Invariant C15 — Provenance Integrity

For material provenance:

```text
UnauthorizedModification(Provenance)
    =>
DetectableIntegrityFailure
```

Meaning:

> **Unauthorized modification of material provenance records must be detectable.**

This establishes tamper evidence.

It does not claim that integrity alone proves substantive truth.

---

## 8.23 What Decision Provenance Proves — and What It Does Not

Decision provenance can establish that:

* a specific action was proposed;
* a specific actor originated it;
* a defined governance state was evaluated;
* a specific decision was produced;
* a specific authorization was bound;
* continuity was evaluated before consequence;
* a specific enforcement point admitted execution;
* a specific route was used;
* a specific executor performed the mutation;
* an execution receipt exists;
* an outcome is linked to the execution;
* the chain can later be reconstructed.

Decision provenance does **not** itself prove that:

* every source fact was true;
* every authority source was legitimate;
* every policy was correct;
* every approval was appropriate;
* every implementation component was uncompromised;
* every route outside the declared architecture boundary was governed.

Provenance preserves the history of governance.

It does not replace governance.

---

## 8.24 Decision Provenance Architecture

![Figure 8 — Decision Provenance: Reconstructing Legitimacy from Action to Outcome](../diagrams/figure-08-decision-provenance.png)

**Figure 8 — Decision Provenance: Reconstructing Legitimacy from Proposed Action to Outcome.**

---

## 8.25 Closing the Reference Architecture

The complete runtime governance path is now:

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
Decision Provenance
```

Each stage answers a separate control question:

| Stage               | Core Question                                                                    |
| ------------------- | -------------------------------------------------------------------------------- |
| Admissibility       | Should this action be permitted under the current governance state?              |
| Binding             | Exactly what action, resource, executor, and state does the decision apply to?   |
| Continuity          | Is that authorization still legitimate now?                                      |
| Enforcement         | Is consequence technically dependent on satisfying governance?                   |
| Route Closure       | Can an alternate path create the same consequence without equivalent governance? |
| Execution           | What protected mutation actually occurred?                                       |
| Decision Provenance | Can the legitimacy and outcome of the consequence be reconstructed?              |

The architecture therefore moves from:

```text
Intelligence proposes
```

to:

```text
Governance determines admissibility
```

to:

```text
Binding makes the decision specific
```

to:

```text
Continuity keeps it current
```

to:

```text
Enforcement makes it controlling
```

to:

```text
Route closure prevents alternate bypass
```

to:

```text
Execution creates consequence
```

to:

```text
Provenance establishes accountability
```

The resulting architectural principle is:

> **A consequential AI action should become real only when the enterprise can establish that the action is admissible, precisely bound, still current, structurally enforced, non-bypassable within the declared system boundary, and reconstructable afterward.**

This completes the core reference architecture.

The next phase is implementation: expressing these architectural claims as contracts, invariants, tests, and a concrete bank-transfer reference scenario.



---

### 1. Final Architectural Invariants

A single authoritative table listing **C1–C15**, with:

* ID
* invariant name
* one-line purpose
* originating section

That removes the inconsistency we found with the stale mid-document table and gives readers one definitive index.

Example:

| ID  | Invariant                           | Purpose                                                                           | Section |
| --- | ----------------------------------- | --------------------------------------------------------------------------------- | ------- |
| C1  | No Direct Consequence               | AI-originated protected actions require governance before mutation.               | 2       |
| C2  | No Inherited Admissibility          | Material state changes invalidate prior admissibility.                            | 3       |
| C3  | Exact Action Binding                | Execution must match the bound action.                                            | 4       |
| C4  | Executor Binding                    | Execution must occur through the bound executor.                                  | 4       |
| C5  | Bounded Authorization Lifetime      | Authorization must remain within its permitted lifetime.                          | 4       |
| C6  | Single-Use Where Required           | Consumed single-use authorization cannot be reused.                               | 4       |
| C7  | Governance Continuity               | Material governance changes prevent inherited execution permission.               | 5       |
| C8  | Commit-Time Revalidation            | Protected mutation requires valid continuity at commit time.                      | 5       |
| C9  | Enforcement Required                | Protected mutation requires governance-aware enforcement.                         | 6       |
| C10 | Fail Closed at Enforcement Boundary | Missing or invalid execution proof cannot permit mutation.                        | 6       |
| C11 | Route Closure                       | Every identified consequence-bearing path must be governed.                       | 7       |
| C12 | No Alternate Consequence Path       | Any ungoverned alternate route means closure has failed.                          | 7       |
| C13 | Provenance Completeness             | Successful protected mutation must leave reconstructable governance lineage.      | 8       |
| C14 | Decision-to-Execution Linkage       | Execution must link back to decision, authorization, continuity, and enforcement. | 8       |
| C15 | Provenance Integrity                | Unauthorized modification of provenance must be detectable.                       | 8       |
   

### 2. Architecture Completion Status

Then immediately below it:

```text
Proposed Action
      ↓
Admissibility       ✓ Section 3
      ↓
Binding             ✓ Section 4
      ↓
Continuity          ✓ Section 5
      ↓
Enforcement         ✓ Section 6
      ↓
Route Closure       ✓ Section 7
      ↓
Execution
      ↓
Decision Provenance ✓ Section 8
```


> **Core Reference Architecture v0.1: Complete**

---

## Continue

**Next:** [Return to Architecture Index](README.MD)

**Return to Main Repository:** [The Agentic AI Governance Control Plane](../README.MD)
