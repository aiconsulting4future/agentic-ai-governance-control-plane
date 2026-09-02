# Specifications

## The Agentic AI Governance Control Plane

The specification layer translates the reference architecture into explicit implementation obligations.

The architecture asks:

> **Can this action, by this actor, under these conditions, become real now?**

The specifications define the contracts and invariants required to answer that question consistently across the full governance lifecycle.

---

## Specification Flow

```text
Architectural Invariants
        ↓
Action Contract
        ↓
Runtime Admissibility
        ↓
Execution Authorization
        ↓
Continuity Contract
        ↓
Enforcement Contract
        ↓
Route Closure
        ↓
Provenance Contract
```

These documents do not introduce a separate architecture.

They formalize the control obligations already defined in the eight-document reference architecture.

---

## Start Here

| Specification | Core Question | Primary Architecture Link |
|---|---|---|
| [Architectural Invariants](invariants.md) | What conditions must the implementation always preserve? | Sections 2–8 |
| [Action Contract](action-contract.md) | What exactly is being proposed? | Sections 2–4 |
| [Execution Authorization](execution-authorization.md) | What exact action, resource, executor, and governance state has been authorized? | Section 4 |
| [Continuity Contract](continuity-contract.md) | Is that authorization still legitimate now? | Section 5 |
| [Enforcement Contract](enforcement-contract.md) | Is protected mutation technically dependent on satisfying governance? | Section 6 |
| [Provenance Contract](provenance-contract.md) | Can the legitimacy and outcome of the consequence be reconstructed? | Section 8 |

---

# 1. Architectural Invariants

**File:** [`invariants.md`](invariants.md)

The invariant specification defines the fifteen architectural conditions that future implementations and tests must preserve.

```text
C1  — No Direct Consequence
C2  — No Inherited Admissibility
C3  — Exact Action Binding
C4  — Executor Binding
C5  — Bounded Authorization Lifetime
C6  — Single-Use Where Required
C7  — Governance Continuity
C8  — Commit-Time Revalidation
C9  — Enforcement Required
C10 — Fail Closed at Enforcement Boundary
C11 — Route Closure
C12 — No Alternate Consequence Path
C13 — Provenance Completeness
C14 — Decision-to-Execution Linkage
C15 — Provenance Integrity
```

The invariants are the bridge between architectural claims and future executable tests.

---

# 2. Action Contract

**File:** [`action-contract.md`](action-contract.md)

The Action Contract defines the canonical representation of a proposed consequence-bearing action.

It answers:

> **What exactly is the system proposing to make real?**

It defines concepts such as:

```text
action_id
actor
action_type
protected resource
material parameters
consequence metadata
material fields
canonicalization
action hash
immutability
```

The Action Contract is not execution authority.

```text
Valid Action Contract
    ≠
Admissible Action
```

and:

```text
Admissible Action
    ≠
Executable Action
```

---

# 3. Execution Authorization

**File:** [`execution-authorization.md`](execution-authorization.md)

The Execution Authorization specification defines how an `ALLOW` decision becomes a bounded authorization.

It answers:

> **What exact action, on what resource, by which executor, under which validated governance state, may proceed toward execution — and for how long?**

It binds:

```text
decision
action
action hash
resource
executor
governance-state references
policy version
approval
validity window
replay semantics
integrity proof
```

The central rule remains:

```text
ALLOW ≠ EXECUTE
```

Instead:

```text
ALLOW
    ↓
ELIGIBLE_FOR_BINDING
    ↓
BOUND AUTHORIZATION
```

A bounded authorization must still survive continuity and enforcement.

---

# 4. Continuity Contract

**File:** [`continuity-contract.md`](continuity-contract.md)

The Continuity Contract closes the time-of-check / time-of-use gap between authorization and consequence.

It answers:

> **Is that authorization still legitimate now?**

The continuity layer evaluates whether the governance conditions that justified the authorization remain valid at the consequence boundary.

Relevant dimensions include:

```text
Identity / Standing
Authority
Evidence
Scope / Delegation
Policy
Risk
Human Approval
Executor State
Protected Resource State
Authorization Lifetime
Replay / Consumption State
```

Possible outcomes are:

```text
VALID
REVALIDATE
HOLD
ESCALATE
REJECT
```

Only `VALID` may proceed toward enforcement.

---

# 5. Enforcement Contract

**File:** [`enforcement-contract.md`](enforcement-contract.md)

The Enforcement Contract defines how governance becomes technically controlling rather than merely advisory.

It answers:

> **Is the executor technically prevented from creating consequence unless the required governance conditions are satisfied?**

The enforcement point verifies, as required:

```text
authorization integrity
action binding
resource binding
executor binding
tenant / environment
authorization lifetime
replay state
continuity
commit-time state
```

The governing principle is:

> **A governance decision is not controlling merely because it exists. It becomes controlling when protected execution is technically dependent on satisfying it.**

Only:

```text
COMMIT
```

may permit protected mutation.

---

# 6. Provenance Contract

**File:** [`provenance-contract.md`](provenance-contract.md)

The Provenance Contract defines the accountability structure for the complete governance chain.

It answers:

> **Can the legitimacy and outcome of the consequence be reconstructed afterward?**

The canonical decision-to-outcome lineage is:

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

The governing distinction is:

```text
Logging records activity.
Provenance reconstructs legitimacy.
```

The provenance layer preserves the relationships among:

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

without requiring storage of private model chain-of-thought.

---

# How the Specifications Map to the Architecture

The specifications are derived directly from the eight-document reference architecture.

| Architecture Section | Specification Role |
|---|---|
| [Section 1 — Why This Capstone Exists](../docs/01-why-this-capstone-exists.md) | Defines the overall control chain and architecture boundaries |
| [Section 2 — From Model Risk to Consequence Risk](../docs/02-from-model-risk-to-consequence-risk.md) | Establishes protected consequence and C1 |
| [Section 3 — Runtime Admissibility](../docs/03-runtime-admissibility.md) | Defines governance state, decision effects, and C2 |
| [Section 4 — Binding](../docs/04-binding.md) | Defines action/resource/executor binding and C3–C6 |
| [Section 5 — Continuity](../docs/05-continuity.md) | Defines temporal legitimacy and C7–C8 |
| [Section 6 — Enforcement](../docs/06-enforcement.md) | Defines structurally mandatory execution control and C9–C10 |
| [Section 7 — Route Closure](../docs/07-route-closure.md) | Defines system-level path closure and C11–C12 |
| [Section 8 — Decision Provenance](../docs/08-decision-provenance.md) | Defines accountability, linkage, integrity, and C13–C15 |

---

# Important Architecture Boundaries

The specification layer preserves several distinctions that must not be collapsed.

```text
Reasoning
    ≠
Execution Authority
```

```text
Capability
    ≠
Authority
```

```text
ALLOW
    ≠
EXECUTE
```

```text
Bound
    ≠
Still Valid
```

```text
Continuity VALID
    ≠
COMMIT
```

```text
Governed Primary Route
    ≠
Governed System
```

```text
Execution Receipt
    ≠
Complete Decision Provenance
```

These distinctions are fundamental to the architecture.

---

# Reference Scenario

The specifications are applied end-to-end in the repository's reference scenario:

## [Bank Transfer Reference Scenario](../examples/bank-transfer/README.md)

The scenario follows:

```text
Treasury-Agent-01
      ↓
₹250,000 Transfer
      ↓
Action Contract
      ↓
Runtime Admissibility
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

The scenario demonstrates both the successful path and failure conditions corresponding to invariants C1–C15.

For readers who want to move from architecture to practical application, this is the recommended next document after the specifications.

---

# Recommended Reading Paths

### Architecture First

```text
docs/README.md
      ↓
Sections 1–8
      ↓
specifications/README.md
      ↓
Bank Transfer Reference Scenario
```

### Implementation First

```text
invariants.md
      ↓
action-contract.md
      ↓
execution-authorization.md
      ↓
continuity-contract.md
      ↓
enforcement-contract.md
      ↓
provenance-contract.md
      ↓
Bank Transfer Reference Scenario
```

### Practical Example First

```text
Bank Transfer Reference Scenario
      ↓
Follow links back to individual contracts
      ↓
Follow links back to architecture sections
```

---

# Specification Status

```text
Reference Architecture v0.1       COMPLETE
Architectural Invariants C1–C15   COMPLETE
Action Contract                    COMPLETE
Execution Authorization            COMPLETE
Continuity Contract                COMPLETE
Enforcement Contract               COMPLETE
Provenance Contract                COMPLETE
Bank-Transfer Specification        COMPLETE
Reference Implementation           PLANNED
Executable Invariant Tests         PLANNED
```

The specification layer is now complete for **The Agentic AI Governance Control Plane v0.1**.

---

## Continue

**Practical Reference:** [Bank Transfer Reference Scenario](../examples/bank-transfer/README.md)

**Architecture:** [Reference Architecture Index](../docs/README.md)

**Return to Main Repository:** [The Agentic AI Governance Control Plane](../README.md)
