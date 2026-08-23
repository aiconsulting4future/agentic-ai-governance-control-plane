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

| Action                      | Consequence Characteristics                      |
| --------------------------- | ------------------------------------------------ |
| Draft an internal email     | low persistence, reversible                      |
| Read a customer record      | confidentiality-sensitive                        |
| Update CRM notes            | state mutation, usually reversible               |
| Approve a refund            | financial consequence                            |
| Create a privileged account | high privilege, security-sensitive               |
| Transfer funds              | financial, potentially irreversible              |
| Delete production data      | integrity/availability, potentially irreversible |

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