# Action Contract

## The Agentic AI Governance Control Plane

**Version:** v0.1  
**Status:** Draft Specification  
**Specification Type:** Proposed Action Data Contract

---

## 1. Purpose

This specification defines the canonical representation of a **proposed consequence-bearing action** before it enters runtime admissibility, binding, continuity, enforcement, and provenance.

The action contract answers one foundational question:

> **What exactly is the AI system proposing to make real?**

The contract exists because governance cannot evaluate, bind, revalidate, enforce, or later reconstruct an action that is only represented as vague intent, free-form text, or transient orchestration state.

A proposed action must therefore be converted into an explicit, structured, machine-readable object before it can become eligible for governance.

Conceptually:

```text
Reasoning / Planning
        ↓
Proposed Intent
        ↓
ACTION CONTRACT
        ↓
Runtime Admissibility
```

The action contract is not an execution authorization.

It represents the **thing being proposed**.

---

## 2. Architectural Role

The action contract sits at the boundary between the intelligence plane and the governance decision plane.

```text
INTELLIGENCE PLANE

Reasoning
Retrieval
Planning
Memory
Agent Coordination
        ↓
Proposed Intent
        ↓
──────────────────────────────
ACTION CONTRACT
──────────────────────────────
        ↓
GOVERNANCE DECISION PLANE

Admissibility
```

The intelligence plane may create, recommend, or populate an action contract.

It does **not** thereby gain permission to execute that action.

The architecture maintains the separation:

```text
Proposed Action
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

## 3. Design Objectives

The action contract should provide enough structure to support:

1. deterministic governance evaluation;
2. exact action binding;
3. material-change detection;
4. stable identity across the governance lifecycle;
5. canonicalization;
6. integrity checking;
7. provenance;
8. replay-safe references;
9. tenant and environment isolation;
10. domain-specific extension without changing the core architecture.

The contract should remain:

- vendor-neutral;
- transport-neutral;
- model-neutral;
- orchestration-framework-neutral;
- domain-extensible.

---

## 4. Canonical Action Object

A conceptual action object is:

```json
{
  "action_id": "ACT-2041",
  "actor": {
    "actor_id": "Treasury-Agent-01",
    "actor_type": "ai_agent",
    "tenant": "Enterprise-A",
    "environment": "production"
  },
  "action_type": "transfer_funds",
  "resource": {
    "resource_type": "bank_account",
    "resource_id": "Corporate-Account-01"
  },
  "parameters": {
    "beneficiary_id": "Vendor-ABC",
    "amount_minor": 25000000,
    "currency": "INR"
  },
  "consequence": {
    "class": "financial",
    "reversibility": "low",
    "persistence": "high"
  },
  "requested_at": "2026-08-24T08:00:00Z",
  "material_fields": [
    "actor.actor_id",
    "action_type",
    "resource.resource_type",
    "resource.resource_id",
    "parameters.beneficiary_id",
    "parameters.amount_minor",
    "parameters.currency",
    "actor.tenant",
    "actor.environment"
  ],
  "schema_version": "0.1"
}
```

The fields above are illustrative but define the minimum architectural shape expected by this specification.

---

# 5. Required Top-Level Fields

## 5.1 `action_id`

```text
action_id
```

A globally unique or sufficiently collision-resistant identifier for the proposed action instance.

Example:

```text
ACT-2041
```

### Requirement

The action identifier **MUST** remain stable across the governance lifecycle.

It should be usable to correlate:

```text
action
decision
authorization
continuity
enforcement
execution
outcome
```

### Constraint

The same `action_id` **MUST NOT** silently refer to two materially different actions.

If the action is materially changed, the implementation should either:

- create a new `action_id`, or
- create a new immutable action version linked to the prior action.

The implementation must not overwrite the original material representation in place.

---

## 5.2 `actor`

The actor identifies the principal originating the proposed action.

Conceptually:

```json
{
  "actor_id": "Treasury-Agent-01",
  "actor_type": "ai_agent",
  "tenant": "Enterprise-A",
  "environment": "production"
}
```

### Required fields

```text
actor_id
actor_type
tenant
environment
```

### `actor_id`

Stable identity of the originating principal.

Examples:

```text
Treasury-Agent-01
Procurement-Agent-03
Human-User-884
Workflow-Service-11
```

### `actor_type`

Examples:

```text
ai_agent
human
service
workflow
multi_agent_system
delegated_executor
```

### `tenant`

Identifies the enterprise, business unit, customer, or isolation boundary to which the action belongs.

### `environment`

Examples:

```text
development
test
staging
production
```

### Requirement

Identity, tenant, and environment **MUST NOT** be inferred only from surrounding prompt text when they are material to governance.

They should exist as explicit contract fields or authoritative execution-context references.

---

## 5.3 `action_type`

A stable, explicit semantic operation identifier.

Example:

```text
transfer_funds
```

Other examples:

```text
create_vendor
update_customer_record
approve_refund
delete_dataset
deploy_release
create_privileged_account
send_external_message
```

### Requirement

The action type **MUST** identify the material operation being proposed.

Avoid ambiguous values such as:

```text
execute
process
do_task
run_action
```

unless an implementation provides a stricter domain-specific subtype.

---

## 5.4 `resource`

The protected or governance-relevant enterprise resource affected by the proposed action.

Conceptually:

```json
{
  "resource_type": "bank_account",
  "resource_id": "Corporate-Account-01"
}
```

### Required fields

```text
resource_type
resource_id
```

### Optional extension fields

An implementation may include:

```text
resource_version
resource_owner
resource_region
resource_classification
resource_namespace
```

### Requirement

Where the consequence is resource-specific, the resource **MUST** be explicit enough for:

- governance evaluation;
- exact binding;
- enforcement verification;
- provenance reconstruction.

---

# 6. Action Parameters

The `parameters` object contains the material operation-specific values.

For a bank transfer:

```json
{
  "beneficiary_id": "Vendor-ABC",
  "amount_minor": 25000000,
  "currency": "INR"
}
```

For a customer-record update:

```json
{
  "customer_id": "CUST-8841",
  "field": "credit_limit",
  "new_value_minor": 5000000,
  "currency": "INR"
}
```

For a production deployment:

```json
{
  "service_id": "payments-api",
  "release_id": "rel-2026-08-24-17",
  "target_environment": "production"
}
```

### Requirement

All material operation parameters **MUST** be represented explicitly.

Material parameters **MUST NOT** exist only inside:

- natural-language prompts;
- chain-of-thought;
- transient tool-call strings;
- UI labels;
- model memory;
- hidden orchestration context.

---

# 7. Monetary Representation

Where money is involved, avoid ambiguous floating-point representations.

Preferred representation:

```json
{
  "amount_minor": 25000000,
  "currency": "INR"
}
```

For INR:

```text
25000000 paise
=
₹250,000.00
```

### Requirement

The implementation **SHOULD** use:

```text
integer minor units
+
explicit currency code
```

rather than:

```text
250000.00
```

as a binary floating-point value.

This reduces canonicalization ambiguity and action-hash instability.

---

# 8. Consequence Metadata

The action contract may include a consequence descriptor.

Conceptually:

```json
{
  "class": "financial",
  "impact": "high",
  "reversibility": "low",
  "persistence": "high",
  "privilege": "standard"
}
```

Possible dimensions include:

```text
financial impact
reversibility
persistence
privilege
confidentiality
integrity
availability
propagation
recoverability
regulatory effect
```

### Requirement

The consequence descriptor supports governance evaluation.

It does **not** itself determine admissibility.

The final risk and autonomy decision belongs to the governance state and policy evaluation.

---

# 9. `requested_at`

The time at which the proposed action contract was created.

Recommended representation:

```text
RFC 3339 / ISO 8601 UTC
```

Example:

```text
2026-08-24T08:00:00Z
```

### Requirement

`requested_at` establishes proposal time.

It does **not** prove:

- authority freshness;
- evidence freshness;
- approval freshness;
- authorization validity;
- or execution legitimacy.

Those properties are evaluated elsewhere in the architecture.

---

# 10. Schema Version

Every action contract should identify its schema version.

Example:

```json
{
  "schema_version": "0.1"
}
```

### Requirement

A material schema interpretation change **MUST NOT** occur silently.

Canonicalization, validation, and action hashing depend on stable field semantics.

---

# 11. Material Fields

A central concept in the action contract is the distinction between:

```text
material fields
```

and:

```text
non-material metadata
```

A material field is any field whose change can alter:

- the nature of the action;
- governance outcome;
- resource affected;
- actor identity;
- scope;
- financial or operational consequence;
- binding;
- authorization;
- enforcement;
- or audit meaning.

For the bank-transfer example:

```text
actor.actor_id
action_type
resource.resource_type
resource.resource_id
parameters.beneficiary_id
parameters.amount_minor
parameters.currency
actor.tenant
actor.environment
```

would normally be material.

Possible non-material metadata may include:

```text
display_label
ui_sort_order
presentation_hint
localized_description
```

provided these cannot alter execution semantics.

### Requirement

The implementation **MUST** define the material field set for each action type or derive it deterministically from an action schema.

Materiality **MUST NOT** depend on ad hoc LLM judgment at execution time.

---

# 12. Material Change

A material change occurs when any field that affects action identity, governance, consequence, or execution semantics changes.

Conceptually:

```text
MaterialChange(A_old, A_new)
    =
TRUE
```

when:

```text
AnyMaterialField(A_old)
    ≠
CorrespondingMaterialField(A_new)
```

### Architectural effect

A material change may require:

```text
new admissibility evaluation
new action version
new binding
new authorization
new approval
new continuity state
```

depending on where the change occurs.

### Relationship to C2

This contract operationalizes:

```text
C2 — No Inherited Admissibility
```

A previous governance decision must not silently survive a material action change.

---

# 13. Canonicalization

Before hashing or exact-action comparison, the action must have a deterministic canonical representation.

Conceptually:

```text
CanonicalAction =
    Canonicalize(ActionContract)
```

Canonicalization should define at minimum:

```text
field ordering
string normalization
Unicode normalization
number representation
currency representation
timestamp representation
null semantics
optional-field semantics
array ordering where relevant
boolean representation
material-field inclusion rules
schema-version handling
```

### Requirement

The same material action **MUST** produce the same canonical representation.

Two materially different actions **MUST NOT** be treated as identical because of inconsistent normalization.

---

# 14. Action Hash

Once canonicalized:

```text
ActionHash =
    H(CanonicalAction)
```

Example:

```text
sha256:9f0c...
```

### Requirement

The action hash should be suitable for:

- exact action binding;
- approval binding;
- execution authorization;
- tamper detection;
- provenance linkage.

### Important distinction

```text
ActionHash
    ≠
Execution Authorization
```

The hash identifies the action representation.

It does not grant permission.

---

# 15. Immutable Action Representation

Once an action has entered governance evaluation, its material representation should be treated as immutable.

If a material change is required:

```text
Action A
    ↓
Material Change
    ↓
Action A'
```

the system should create a new action version or action instance.

### Requirement

An implementation **MUST NOT** mutate the material contents of a governed action in place while preserving the same governance references as if nothing changed.

---

# 16. Optional Action Versioning

An implementation may use explicit action versions.

Example:

```json
{
  "action_id": "ACT-2041",
  "action_version": 3
}
```

Then:

```text
ACT-2041:v1
ACT-2041:v2
ACT-2041:v3
```

may represent successive proposals.

### Requirement

If versioning is used, each version's material representation must remain reconstructable.

A later version **MUST NOT** overwrite the provenance of an earlier governed version.

---

# 17. Domain Extension

The action contract defines the architectural core.

Domain-specific actions may extend it.

Example:

```json
{
  "domain": "treasury",
  "action_type": "transfer_funds",
  "parameters": {
    "beneficiary_id": "Vendor-ABC",
    "amount_minor": 25000000,
    "currency": "INR",
    "payment_reference": "INV-8842"
  }
}
```

Another domain:

```json
{
  "domain": "iam",
  "action_type": "create_privileged_account",
  "parameters": {
    "principal_id": "USR-4482",
    "role": "production_admin",
    "duration_seconds": 3600
  }
}
```

### Requirement

Domain extension **MAY** add fields.

It **MUST NOT** weaken the architectural semantics of:

- actor identity;
- action identity;
- resource identity;
- materiality;
- canonicalization;
- immutability.

---

# 18. Validation Rules

Before an action enters admissibility, the action contract should pass structural validation.

A conceptual validation sequence:

```text
1. Schema recognized?
2. Required fields present?
3. action_id valid?
4. actor identity present?
5. tenant present?
6. environment present?
7. action_type recognized?
8. protected resource resolvable?
9. parameters structurally valid?
10. consequence descriptor valid where required?
11. material-field definition available?
12. canonicalization possible?
```

### Possible validation outcomes

```text
VALID
INVALID_SCHEMA
MISSING_REQUIRED_FIELD
UNKNOWN_ACTION_TYPE
INVALID_RESOURCE
INVALID_PARAMETER
UNRESOLVED_MATERIALITY
CANONICALIZATION_FAILURE
```

### Requirement

A structurally invalid action contract **MUST NOT** proceed as though it were an admissible action.

---

# 19. Validation Is Not Admissibility

This distinction is critical.

```text
ActionContractValid(A)
    ≠
Admissible(A)
```

Structural validity means:

> the action is well-formed enough to evaluate.

Admissibility means:

> governance permits the action under the current governance state.

A transfer can be perfectly well-formed and still be:

```text
HOLD
ESCALATE
DENY
```

---

# 20. Relationship to Architectural Invariants

The action contract directly supports several invariants.

## C1 — No Direct Consequence

The contract establishes a structured object that can be routed into governance before protected mutation.

```text
AI-originated action
    ↓
Action Contract
    ↓
Governance
```

The contract does not itself enforce C1, but it provides the governed action representation required to do so.

---

## C2 — No Inherited Admissibility

Material action fields make change detectable.

```text
MaterialChange(A_t0, A_t1)
    =>
Invalidate(previous decision)
```

The action contract therefore provides the comparison surface required by C2.

---

## C3 — Exact Action Binding

Canonicalization and the action hash provide the basis for:

```text
ExecutionActionHash
    =
BoundActionHash
```

C3 depends on the action contract being deterministic enough to establish exact material identity.

---

# 21. Bank-Transfer Reference Action

The reference implementation will use:

```text
Transfer ₹250,000
from Corporate-Account-01
to Vendor-ABC
```

A complete action contract may be:

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

Its canonical representation is then hashed:

```text
ActionHash =
    H(Canonical(ActionContract))
```

Illustrative result:

```text
sha256:...
```

That hash will later become a binding input to the execution authorization.

---

# 22. Example Material-Change Cases

## Case A — Amount changes

Original:

```text
₹250,000
```

Modified:

```text
₹350,000
```

Result:

```text
MaterialChange = TRUE
```

Prior admissibility and binding must not be silently inherited.

---

## Case B — Beneficiary changes

Original:

```text
Vendor-ABC
```

Modified:

```text
Vendor-XYZ
```

Result:

```text
MaterialChange = TRUE
```

---

## Case C — Environment changes

Original:

```text
staging
```

Modified:

```text
production
```

Result:

```text
MaterialChange = TRUE
```

The same operation in a different execution environment is not necessarily the same governed action.

---

## Case D — Display label changes

Original:

```text
"Vendor payment"
```

Modified:

```text
"Invoice settlement"
```

If `display_label` is explicitly defined as non-material:

```text
MaterialChange = FALSE
```

provided the change cannot influence execution semantics.

---

# 23. Example Invalid Contracts

## Missing resource

```json
{
  "action_id": "ACT-2041",
  "action_type": "transfer_funds",
  "parameters": {
    "beneficiary_id": "Vendor-ABC",
    "amount_minor": 25000000,
    "currency": "INR"
  }
}
```

If the source account is required to establish the protected resource:

```text
INVALID_RESOURCE
```

---

## Ambiguous amount

```json
{
  "amount": 250000
}
```

Without explicit representation and currency:

```text
INVALID_PARAMETER
```

for an implementation requiring minor-unit + currency semantics.

---

## Unknown action type

```json
{
  "action_type": "do_payment_thing"
}
```

If the action type is not mapped to a known governed action schema:

```text
UNKNOWN_ACTION_TYPE
```

---

# 24. Reference Validation Pseudocode

Conceptually:

```text
ValidateActionContract(A):

    require schema_version supported
    require action_id present
    require actor.actor_id present
    require actor.tenant present
    require actor.environment present
    require action_type known
    require resource valid
    require parameters valid for action_type
    require material-field schema available
    require canonicalization succeeds

    return VALID
```

Again:

```text
VALID
    ≠
ALLOW
```

After validation:

```text
VALID Action Contract
        ↓
Runtime Admissibility
```

---

# 25. Action Contract State

A simple lifecycle is:

```text
DRAFT
  ↓
VALIDATING
  ├──→ INVALID
  └──→ VALID
          ↓
      GOVERNANCE_EVALUATION
```

Once governance evaluation begins, material mutation should require a new action version or action instance.

---

# 26. Provenance Requirements

The action contract should remain reconstructable after execution.

At minimum, provenance should be able to recover:

```text
action_id
actor identity
action_type
resource
material parameters
schema version
action hash
proposal timestamp
material-field definition or schema reference
```

The full governance lineage belongs to Decision Provenance.

---

# 27. Security Considerations

The action contract itself can become a security boundary because downstream governance may rely upon it.

Implementations should therefore consider:

- contract tampering;
- ambiguous serialization;
- Unicode confusion;
- numeric ambiguity;
- omitted fields;
- duplicate JSON keys;
- schema downgrade;
- tenant confusion;
- environment confusion;
- resource aliasing;
- parameter smuggling;
- canonicalization disagreement.

A contract accepted by one component but interpreted differently by another can invalidate exact binding.

---

# 28. Non-Claims

The action contract does **not** prove:

- that the proposed action is correct;
- that the model reasoning was correct;
- that the actor has current authority;
- that evidence is valid;
- that policy permits the action;
- that risk is acceptable;
- that approval exists;
- that execution is authorized;
- that route closure exists;
- or that the eventual outcome is legitimate.

It establishes only a stable representation of **what is being proposed**.

---

# 29. Specification Boundary

This specification stops at the proposed-action representation.

The next contract defines how a successful admissibility decision becomes a bounded execution authorization.

```text
Action Contract
      ↓
Runtime Admissibility
      ↓
ALLOW
      ↓
Execution Authorization Contract
```

The next specification is:

> **Execution Authorization**

---

# 30. Specification Status

```text
Reference Architecture v0.1       COMPLETE
Architectural Invariants C1–C15   COMPLETE
Action Contract v0.1              COMPLETE
Execution Authorization           NEXT
Governance State Models            PLANNED
Bank-Transfer Demonstrator         PLANNED
Executable Tests                   PLANNED
```

This document is the canonical action-contract specification for **The Agentic AI Governance Control Plane v0.1**.

---

## Related Specifications

- [Architectural Invariants](invariants.md)
- [Reference Architecture Index](../docs/README.md)
- [Section 2 — From Model Risk to Consequence Risk](../docs/02-from-model-risk-to-consequence-risk.md)
- [Section 3 — Runtime Admissibility](../docs/03-runtime-admissibility.md)
- [Section 4 — Binding](../docs/04-binding.md)

**Return to Main Repository:** [The Agentic AI Governance Control Plane](../README.md)
