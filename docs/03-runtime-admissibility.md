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

| Symbol | Governance Dimension   |
| ------ | ---------------------- |
| `C_t`  | Capability             |
| `I_t`  | Identity and Standing  |
| `U_t`  | Authority              |
| `E_t`  | Evidence and Context   |
| `S_t`  | Scope and Delegation   |
| `P_t`  | Policy                 |
| `R_t`  | Risk and Reversibility |
| `H_t`  | Human Approval         |

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