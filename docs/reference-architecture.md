# The Agentic AI Governance Control Plane

## A Reference Architecture for Runtime Admissibility, Binding, Continuity, Enforcement, Route Closure, and Decision Provenance

**Version:** v0.1-draft  
**Status:** Reference Architecture — Work in Progress

---

## Abstract

Enterprise AI governance becomes materially different when an AI system can do more than generate text.

Once an AI agent can invoke tools, mutate enterprise systems, initiate financial transactions, modify records, change permissions, trigger workflows, or create other durable consequences, the principal governance question is no longer only whether the model produced a correct or safe output.

The governing question becomes:

> **Can this action, by this actor, under these conditions, become real now?**

This reference architecture treats that question as a runtime systems problem.

It separates the lifecycle of a consequence-bearing AI action into distinct architectural stages:

1. **Intelligence** — reasoning, retrieval, planning, memory, and action proposal.
2. **Admissibility** — determining whether the proposed action is permitted under the current governance state.
3. **Binding** — converting an admissibility decision into a bounded authorization for one exact action, resource, executor, and validated state.
4. **Continuity** — determining whether the conditions that justified the authorization still hold at the consequence boundary.
5. **Enforcement** — ensuring that consequence-bearing execution can occur only through a valid governance decision.
6. **Route Closure** — establishing that alternate paths cannot bypass equivalent governance enforcement.
7. **Execution** — creating the intended enterprise consequence.
8. **Provenance** — preserving enough evidence to reconstruct why the action was permitted, how it was executed, and what outcome followed.

The architecture is deliberately vendor-neutral. It is intended to be expressed through explicit data contracts, state transitions, invariants, enforcement mechanisms, and executable tests.

It does **not** claim to be a universal compliance framework, a legal certification model, or proof that every AI system can be made safe. Its purpose is narrower:

> **to define the control conditions under which an AI-originated action may become an enterprise consequence.**

---

# 1. Why This Capstone Exists

This architecture began as a sequence of seven questions about enterprise agentic AI governance.

Each question initially appeared separable:

- What happens when an AI produces the wrong action?
- What if the action is valid but the actor's authority has changed?
- Where should enterprise control sit between AI reasoning and execution?
- Can a multi-agent system reach the correct result while following an invalid path?
- How much autonomy should a system have for a particular consequence?
- What does Zero Trust mean when the subject is an AI agent rather than a human user?
- What evidence is required to reconstruct why a consequence occurred?

Taken independently, these appear to be questions about reliability, authorization, security, autonomy, and auditability.

They converge on a deeper architectural problem:

> **A governance decision has limited value unless it remains authoritative all the way to consequence.**

A system may correctly identify that an action is not permitted and still fail governance if some other route can execute it.

A system may correctly determine that an action is allowed and still fail governance if the action changes after approval.

A system may have valid authority when planning begins and stale authority when execution occurs.

A system may preserve excellent logs while still being unable to establish whether the action was legitimate when it happened.

The core problem is therefore not merely decision quality.

It is **governed consequence creation**.

## 1.1 From Seven Governance Questions to One Runtime Architecture

The seven ideas can be viewed as a progression:

```text
Wrong Action
    ↓
Authority Drift
    ↓
Control Plane
    ↓
Path Correctness
    ↓
Autonomy
    ↓
Zero Trust
    ↓
Decision Provenance
    ↓
RUNTIME GOVERNANCE ARCHITECTURE
```

They converge on one operating question:

> **Can this action, by this actor, under these conditions, become real now?**

<!-- IMAGE PLACEHOLDER: FIGURE 1 -->
![Figure 1 — From Seven Governance Questions to One Runtime Architecture](../diagrams/figure-01-series-to-framework.png)

**Figure 1 — From Seven Governance Questions to One Runtime Architecture.**

## 1.2 The Architectural Shift

A conventional AI application is often represented as:

```text
Input
  ↓
Model
  ↓
Output
```

An agentic enterprise system is materially different:

```text
Input
  ↓
Reasoning / Retrieval / Planning / Memory
  ↓
Proposed Action
  ↓
Tool / API / Workflow
  ↓
Enterprise System
  ↓
State Change
  ↓
Consequence
```

Once an AI system can alter external state, reasoning quality is only one part of the problem.

Enterprise governance must determine:

- whether the system is capable of performing the action safely;
- whether the actor still has valid standing;
- whether current authority exists;
- whether the evidence remains valid;
- whether the action is within scope;
- whether policy permits it;
- whether the consequence is proportionate to the permitted autonomy;
- whether human approval is required;
- whether the exact approved action is the action that will execute;
- whether the validated state remains current at commit time;
- whether the executor is forced to honor the governance result;
- whether another route can bypass that control;
- and whether the full decision-to-outcome chain can later be reconstructed.

This leads to the reference architecture developed in this document.

## 1.3 The Control Chain

The high-level control chain is:

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
Provenance
```

Each stage answers a different question.

| Stage | Governing Question |
|---|---|
| Proposed Action | What does the AI system intend to do? |
| Admissibility | Should this action be permitted under the current governance state? |
| Binding | Exactly which action, resource, executor, and validated state does the decision apply to? |
| Continuity | Do the conditions that justified the decision still hold now? |
| Enforcement | Is execution structurally forced to honor the governance result? |
| Route Closure | Can any alternate consequence-bearing path bypass equivalent control? |
| Execution | What mutation or external consequence actually occurred? |
| Provenance | Can the legitimacy and outcome of the action be reconstructed? |

## 1.4 Architectural Planes

### Intelligence Plane

Responsible for reasoning, retrieval, planning, memory, tool selection, multi-agent coordination, and candidate action generation.

The intelligence plane may recommend or propose. It does not independently create execution authority.

### Governance Decision Plane

Responsible for evaluating capability, identity and standing, authority, evidence, scope, policy, risk and reversibility, and human approval.

Its output is:

```text
ALLOW
HOLD
ESCALATE
DENY
```

### Binding / Control Plane

Responsible for converting an admissibility decision into a bounded authorization tied to the exact action, resource, executor, governance-state references, validity period, replay protection, and integrity proof.

### Enforcement / Execution Plane

Responsible for continuity validation, commit-boundary checks, authorization verification, controlled mutation, and execution receipt generation.

### Accountability Plane

Responsible for preserving decision provenance, authority state, evidence state, policy version, approval state, binding state, continuity outcome, execution receipt, resulting state, and replayability.

## 1.5 Design Principle

> **Governance is not merely the decision to allow or deny. Governance is making that decision binding on consequence.**

Every architectural claim in this document should eventually be one of the following:

1. demonstrable in code;
2. testable through an invariant;
3. enforceable through a system boundary; or
4. explicitly identified as a design principle rather than a proven guarantee.

This capstone is therefore not a summary of seven articles. It consolidates the series into an implementable reference architecture whose claims can later be expressed as system invariants, enforcement mechanisms, and executable tests.

---

# 2. From Model Risk to Consequence Risk

Traditional AI safety and evaluation often begin with the model output. That remains important, but it is insufficient for systems capable of external action.

Suppose a model produces:

```text
"Transfer ₹250,000 to Vendor ABC."
```

At the model layer, possible questions include:

- Is the statement factually correct?
- Was the beneficiary identified correctly?
- Did the model hallucinate?
- Is the recommendation consistent with retrieved evidence?
- Was the reasoning valid?

Those are model and decision-quality questions. They are not yet consequence-governance questions.

The risk changes when the recommendation becomes:

```text
transfer_funds(
    source="Corporate-Account-01",
    beneficiary="Vendor-ABC",
    amount=250000
)
```

At that point the system is no longer merely producing information. It is proposing a state transition.

## 2.1 Output Validity and State Transition Validity Are Different Problems

A simplified model interaction can be written as:

```text
y = M(x)
```

An agentic system introduces an action policy:

```text
a = π(y, c)
```

Execution then changes enterprise state:

```text
S_(t+1) = E(S_t, a)
```

The governance question therefore changes from:

```text
Valid(y)?
```

to:

```text
Permitted(S_t → S_(t+1))?
```

## 2.2 Consequence Governance

For a proposed action `a_t`, current state `S_t`, and governance state `G_t`, define:

```text
Γ(a_t, S_t, G_t) ∈ {ALLOW, HOLD, ESCALATE, DENY}
```

The important separations are:

```text
Desirable(a_t) ≠> Admissible(a_t)
```

and:

```text
Admissible(a_t) ≠> Executable(a_t)
```

A system may conclude that an action is desirable while governance determines that it is not currently admissible.

Likewise, a governance system may determine that an action is admissible while execution must still wait for binding, continuity validation, and enforcement.

## 2.3 Consequence Classes

Relevant consequence dimensions may include:

- financial impact;
- reversibility;
- confidentiality;
- integrity;
- availability;
- legal or regulatory effect;
- privilege;
- persistence;
- propagation;
- recoverability.

| Action | Consequence Characteristics |
|---|---|
| Draft an internal email | low persistence, reversible |
| Read a customer record | confidentiality-sensitive |
| Update CRM notes | state mutation, usually reversible |
| Approve a refund | financial consequence |
| Create a privileged account | high privilege, security-sensitive |
| Transfer funds | financial, potentially irreversible |
| Delete production data | integrity/availability, potentially irreversible |

## 2.4 The Consequence Boundary

The **consequence boundary** is the point at which a proposed action can create externally meaningful state change.

```text
Reasoning
   ↓
Proposed Action
   ↓
Governance
   ↓
Binding
   ↓
Enforcement
-------------------- CONSEQUENCE BOUNDARY
   ↓
Execution
   ↓
Enterprise State Mutation
```

Before the boundary, the system may reason, evaluate, simulate, classify, retrieve, and prepare.

After the boundary, the system can make something real.

<!-- IMAGE PLACEHOLDER: FIGURE 2 -->
![Figure 2 — When Model Risk Becomes Consequence Risk](../diagrams/figure-02-consequence-risk.png)

**Figure 2 — When Model Risk Becomes Consequence Risk.**

## 2.5 Invariant C1 — No Direct Consequence

```text
Protected(a_t) AND AIOrigin(a_t) => Governed(a_t)
```

> **An AI-originated protected action must not mutate protected state without a governance determination.**

This invariant does **not yet prove** that every alternate path to protected state is governed. That stronger requirement belongs to route closure.

## 2.6 Why Logging Is Not Enough

Post-event logs can answer:

> What happened?

They may not answer:

> Was the action entitled to happen under the state that existed at the time of execution?

A governance record should be capable of establishing:

- which action was proposed;
- which actor proposed it;
- what authority existed;
- what evidence applied;
- which policy version governed it;
- what risk state existed;
- whether approval was required;
- who approved;
- whether those conditions remained current;
- which executor performed the action;
- whether the action matched the authorized action;
- and what resulting state followed.

## 2.7 Section 2 Conclusion

> **Reasoning must never be equivalent to execution authority.**

The next question is:

> **What must be true before a proposed action becomes admissible?**

---

# 3. Runtime Admissibility: What Must Be True Before an Action Can Proceed?

Runtime admissibility determines whether a proposed action is permitted under the governance state that exists **now**.

It is not inherited from previous approval, previous identity verification, a valid login, a system role, earlier authority, a previous successful action, or the fact that the AI system is generally trusted.

## 3.1 Governance State

```text
G_t = {
    C_t,
    I_t,
    U_t,
    E_t,
    S_t,
    P_t,
    R_t,
    H_t
}
```

| Symbol | Governance Dimension |
|---|---|
| `C_t` | Capability |
| `I_t` | Identity and Standing |
| `U_t` | Authority |
| `E_t` | Evidence and Context |
| `S_t` | Scope and Delegation |
| `P_t` | Policy |
| `R_t` | Risk and Reversibility |
| `H_t` | Human Approval |

```text
Γ(a_t, G_t) ∈ {ALLOW, HOLD, ESCALATE, DENY}
```

Crucially:

```text
ALLOW ≠ EXECUTE
```

An `ALLOW` decision means only that the action has satisfied the current admissibility conditions and may proceed to **Binding**.

## 3.2 Gate 1 — Capability

```text
C_t(a) ∈ {
    VALID,
    INSUFFICIENT,
    DEGRADED,
    INVALID
}
```

Capability may depend on validated task performance, tool-use reliability, error rates, evaluation results, environment, action class, consequence class, and recent operational observations.

```text
Capability ≠ Authority
```

## 3.3 Gate 2 — Identity and Standing

Identity asks:

> **Which actor is making the request?**

Standing asks:

> **Is that actor currently recognized as eligible to exercise governance-relevant authority?**

```text
I_t(a) ∈ {
    CURRENT,
    UNCERTAIN,
    EXPIRED,
    REVOKED
}
```

Identity and standing must not be collapsed into authentication.

## 3.4 Gate 3 — Authority and Freshness

```text
U_t(a) ∈ {
    CURRENT,
    WEAKENED,
    UNCERTAIN,
    ABSENT,
    REVOKED
}
```

Conceptually:

```text
AuthorityValid =
    f(
        grant,
        scope,
        time,
        state_change,
        consequence
    )
```

A prior authority record establishes historical entitlement. It does not automatically establish present entitlement.

## 3.5 Gate 4 — Evidence and Context

```text
E_t(a) ∈ {
    SUFFICIENT,
    STALE,
    CONFLICTED,
    INCOMPLETE,
    INVALID
}
```

```text
CorrectReasoning ≠ ValidEvidence
```

A model can reason correctly from stale evidence. Governance must therefore treat evidence state as an independent input.

## 3.6 Gate 5 — Scope and Delegation

```text
S_t(a) ∈ {
    IN_SCOPE,
    PARTIAL,
    OUT_OF_SCOPE,
    EXPIRED
}
```

Example:

```text
Delegation:
    Treasury-Agent-01
    may initiate vendor payments
    up to ₹100,000
```

Proposed action:

```text
₹250,000 transfer
```

Therefore:

```text
Authorized(action_type) ≠ Authorized(exact_action)
```

## 3.7 Gate 6 — Policy

```text
P_t(a) ∈ {
    SATISFIED,
    CONDITIONAL,
    VIOLATED,
    UNRESOLVED
}
```

The policy identity and version should be preserved:

```text
policy_id      = TREASURY-PAYMENTS
policy_version = 17
```

## 3.8 Gate 7 — Risk and Reversibility

```text
R_t(a) =
    f(
        Impact,
        Reversibility,
        Persistence,
        Privilege,
        Propagation,
        Recoverability
    )
```

Possible classifications:

```text
LOW
MODERATE
HIGH
CRITICAL
```

> **Greater consequence should generally reduce unreviewed autonomy.**

## 3.9 Gate 8 — Human Approval

```text
H_t(a) ∈ {
    NOT_REQUIRED,
    PRESENT,
    PENDING,
    EXPIRED,
    REVOKED
}
```

Approval should be bound to the exact action, material parameters, approving authority, scope, timestamp, expiration, and relevant state assumptions.

```text
Approved(a) ≠ Approved(a')
```

if `a'` materially differs from `a`.

## 3.10 The Admissibility Function

```text
A_t = Admissible(a_t, G_t)
```

A simplified Boolean concept is:

```text
C AND I AND U AND E AND S AND P AND R AND H
```

but the deterministic governance transition should be explicit:

```text
Γ(a_t, G_t) ∈ {
    ALLOW,
    HOLD,
    ESCALATE,
    DENY
}
```

A practical rule hierarchy:

```text
1. Explicit mandatory violation
       → DENY

2. Required state unresolved, stale, conflicted, or incomplete
       → HOLD

3. Higher authority or human judgment required
       → ESCALATE

4. All required governance conditions valid
       → ALLOW
```

LLMs may assist interpretation, extraction, classification, evidence synthesis, or candidate policy mapping. The final governance state transition should come from explicit rules over governed state.

## 3.11 The Four Outcomes

### ALLOW

```text
ALLOW = ELIGIBLE_FOR_BINDING
```

### HOLD

Execution cannot proceed because a required condition is unresolved or temporarily invalid.

### ESCALATE

The system requires a higher authority or explicit human judgment.

### DENY

The action violates a mandatory governance condition.

## 3.12 Uncertainty Must Change State

> **Absence of evidence must not silently become evidence of validity.**

For example:

```text
Evidence unavailable
        ↓
HOLD
```

Similarly:

```text
Authority uncertain
        ↓
HOLD / ESCALATE
```

## 3.13 Runtime Admissibility State Machine

```text
PROPOSED
   ↓
EVALUATING
   ├──→ ALLOW
   ├──→ HOLD
   ├──→ ESCALATE
   └──→ DENY
```

A `HOLD` or `ESCALATE` may return to evaluation after the required state changes.

A `DENY` cannot enter Binding under the same material action state.

## 3.14 Invariant C2 — No Inherited Admissibility

```text
MaterialChange(a_t, G_t)
    =>
Invalidate(Γ_previous)
```

> **A previous admissibility decision must not automatically remain valid after a material change in the action or governance state.**

Therefore:

```text
ALLOW_t0 ≠ automatically ALLOW_t1
```

## 3.15 Runtime Admissibility Architecture

<!-- IMAGE PLACEHOLDER: FIGURE 3 -->
![Figure 3 — Runtime Admissibility: What Must Be True Before an Action Can Proceed?](../diagrams/figure-03-runtime-admissibility.png)

**Figure 3 — Runtime Admissibility: What Must Be True Before an Action Can Proceed?**

## 3.16 Section 3 Conclusion

Runtime admissibility asks:

> **Should this action be permitted under the current governance state?**

If the result is `ALLOW`, the action becomes:

```text
ALLOW
  ↓
ELIGIBLE_FOR_BINDING
```

The next proof obligation is:

> **Can this decision be made specific to one exact action, resource, executor, and validated state?**

---

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

That is the continuity problem.

---

# Current Architectural Invariants

| ID | Invariant | Purpose |
|---|---|---|
| **C1** | No Direct Consequence | AI-originated protected actions require governance before mutation. |
| **C2** | No Inherited Admissibility | Material state changes invalidate prior admissibility. |
| **C3** | Exact Action Binding | Execution must match the bound action. |
| **C4** | Executor Binding | Execution must occur through the bound executor. |
| **C5** | Bounded Authorization Lifetime | Authorization must remain within its permitted lifetime. |
| **C6** | Single-Use Where Required | Consumed single-use authorization cannot be reused. |

---

# Architecture Status

```text
Proposed Action
      ↓
Admissibility       ✓ Section 3
      ↓
Binding             ✓ Section 4
      ↓
Continuity          → Section 5
      ↓
Enforcement         → Section 6
      ↓
Route Closure       → Section 7
      ↓
Execution
      ↓
Decision Provenance → Section 8
```

The next section is:

> **Section 5 — Continuity: Does the Validated State Still Hold at the Consequence Boundary?**
---

````markdown
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
````

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

* fraud alert raised;
* cybersecurity incident declared;
* beneficiary risk classification increased;
* market volatility threshold crossed;
* system health degraded;
* transaction velocity increased;
* related account placed under investigation.

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

* workload identity;
* certificate validity;
* service principal status;
* runtime attestation;
* environment;
* deployment version;
* security posture;
* tenant context.

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

* revoked authority;
* expired authorization;
* consumed authorization;
* invalid executor;
* prohibited resource state;
* revoked approval.

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

* transactional validation;
* atomic compare-and-set;
* database constraints;
* version checks;
* locks;
* conditional writes;
* short-lived signed proofs;
* policy-engine decisions bound to transaction state.

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

* the relevant identity and standing remain valid;
* authority remains current;
* evidence remains usable;
* scope remains compatible;
* policy remains applicable;
* risk conditions remain acceptable;
* approval remains valid;
* the executor remains acceptable;
* the protected resource remains compatible;
* the authorization has not expired;
* the authorization has not already been consumed;
* no material change requires re-evaluation.

Continuity does **not** by itself prove that:

* the executor is structurally forced to honor the result;
* the protected system will refuse unauthorized mutation;
* another execution route cannot bypass governance;
* the final state mutation occurred exactly as intended.

Those are enforcement and route-closure obligations.

---

## 5.25 Continuity Architecture

<!-- IMAGE PLACEHOLDER: FIGURE 5 -->

<!-- Recommended file: ../diagrams/figure-05-continuity.png -->

![Figure 5 — Continuity: Revalidate Before Consequence](YOUR_IMAGE_URL)

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

````

I would add this **directly after Section 4 and before the current invariant summary**.

Also update the invariant table to include:

```markdown
| **C7** | Governance Continuity | Material governance changes must prevent inherited execution permission. |
| **C8** | Commit-Time Revalidation | Protected mutation requires valid continuity at the commit boundary. |
````

And update the architecture-status block to:

```text
Proposed Action
      ↓
Admissibility       ✓ Section 3
      ↓
Binding             ✓ Section 4
      ↓
Continuity          ✓ Section 5
      ↓
Enforcement         → Section 6
      ↓
Route Closure       → Section 7
      ↓
Execution
      ↓
Decision Provenance → Section 8
```

This section is intentionally substantial because **Continuity is the bridge between authorization and actual consequence**. Section 6 can now focus cleanly on the different problem of structural enforcement rather than mixing temporal revalidation into it.

---

![Figure 5 — Continuity: Does the Validated State Still Hold at the Consequence Boundary?](../diagrams/figure-05-continuity.png)

---

