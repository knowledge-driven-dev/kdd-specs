# Layer 04: Verification

## Closing the Loop: How Do We Know It Works?

---

## Introduction

The Verification layer is the **closing of the specification cycle** in KDD. It answers the most pragmatic question: **How do we verify that the system does what it should?**

This layer is not an appendix or a "nice to have." It is the culmination of all the previous work, where abstract specifications are transformed into **verifiable criteria** that can be automated, audited, and maintained.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   00-Requirements → 01-Domain → 02-Behavior    → 03-Experience              │
│         │               │              │                 │                  │
│         │               │              │                 │                  │
│         └───────────────┴──────────────┴─────────────────┘                  │
│                                   │                                          │
│                                   ▼                                          │
│                           04-Verification                                    │
│                                                                              │
│                        "DID WE BUILD IT RIGHT?"                             │
│                        "DID WE BUILD THE RIGHT THING?"                      │
│                                                                              │
│                           ────────────────────                               │
│                                                                              │
│                        Formal requirements (EARS)                            │
│                        Acceptance criteria (Gherkin)                         │
│                        Complete traceability                                 │
│                                                                              │
│   05-Architecture (ORTHOGONAL — ADRs, Implementation Charter)                │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Philosophy: Executable Specifications

### Why This Layer Closes the Loop

In traditional methodologies, verification is a retrospective exercise: first you build, then you test. KDD inverts this:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   TRADITIONAL APPROACH             KDD APPROACH                             │
│   ────────────────────             ────────────                             │
│                                                                              │
│   1. Write code                    1. Specify behavior                      │
│   2. Test manually                 2. Derive formal requirements            │
│   3. Write tests (sometimes)       3. Define acceptance criteria            │
│   4. Document (almost never)       4. Implement code                        │
│         ↓                          5. Tests ALREADY EXIST                   │
│   Outdated tests                         ↓                                  │
│   Obsolete documentation           Specification = Documentation = Tests    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### The Complete Traceability Principle

In KDD, every line of code must be traceable to a specification:

```
Code → Test → EARS Requirement → Use Case → Business Rule → Objectives
```

If you cannot establish this chain, you are writing code without justification.

---

## The Artifacts of the Verification Layer

### 1. EARS Requirements (Easy Approach to Requirements Syntax)

EARS is a structured syntax for writing unambiguous requirements. Each requirement follows one of these patterns:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   EARS PATTERNS                                                              │
│   ─────────────                                                              │
│                                                                              │
│   UBIQUITOUS (always):                                                       │
│   "The system SHALL [behavior]"                                              │
│   → Behavior that always holds                                               │
│                                                                              │
│   EVENT-DRIVEN (when):                                                       │
│   "WHEN [trigger], the system SHALL [response]"                             │
│   → Response to a specific event                                             │
│                                                                              │
│   STATE-DRIVEN (while):                                                      │
│   "WHILE [state], the system SHALL [behavior]"                              │
│   → Behavior during a state                                                  │
│                                                                              │
│   UNWANTED/IF (if undesired):                                               │
│   "IF [condition], the system SHALL [prevention]"                           │
│   → Handling of undesired situations                                         │
│                                                                              │
│   OPTIONAL/WHERE (where applicable):                                         │
│   "WHERE [condition], the system SHALL [behavior]"                          │
│   → Conditional/optional behavior                                            │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

#### Example: Event-Driven Requirement

```markdown
## REQ-001.1: Order Creation (Event-Driven)

**Pattern**: Event-Driven

WHEN the user submits the order creation form with a valid title,
the system SHALL create a new Order entity
  AND SHALL assign the authenticated user as owner (creator_id)
  AND SHALL set the status to "draft"
  AND SHALL redirect the user to the products configuration screen.

**Traceability**: UC-001-PlaceOrder, steps 7-11
```

#### Example: Unwanted Requirement

```markdown
## REQ-003.3: Single Active Cart (Unwanted/If)

**Pattern**: Unwanted Behavior (If)

IF the Order already has a Cart in state "in_progress",
the system SHALL reject new cart creation
  AND SHALL display message "This order already has a cart in progress"
  AND SHALL offer options to continue or cancel the existing cart.

**Traceability**: UC-003-InitiateCart, extension; BR-CART-002
```

---

### 2. Acceptance Criteria (Gherkin)

Each EARS requirement is decomposed into executable acceptance criteria using Gherkin syntax:

```gherkin
Given a logged-in user on the order creation form
When the user enters title "Should we expand to the European market?"
  And clicks "Place Order"
Then a new Order should be created with status "draft"
  And the user should be redirected to /orders/{id}/products
  And a success message "Order created successfully" should be displayed
```

#### The Anatomy of a Good Criterion

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   GHERKIN STRUCTURE                                                          │
│   ──────────────────                                                         │
│                                                                              │
│   Given [context/preconditions]                                              │
│     → Initial state of the system                                            │
│     → Authenticated user, existing data, etc.                                │
│                                                                              │
│   When [action/trigger]                                                      │
│     → The action that triggers the behavior                                  │
│     → A click, a submit, a system event                                      │
│                                                                              │
│   Then [expected result]                                                     │
│     → What should happen                                                     │
│     → Observable, verifiable, specific                                       │
│                                                                              │
│   And [additional conditions]                                                │
│     → Multiple conditions in any section                                     │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

#### Complete Example with Multiple Scenarios

```gherkin
# REQ-003.3: Single Active Cart

Given order "Test" has a cart in state "in_progress"
When the user attempts to create a new cart
Then error "This order already has a cart in progress" should be displayed
  And options "Continue cart" and "Cancel cart" should be offered
  And no new cart should be created

Given order "Test" has a cart in state "completed"
When the user attempts to create a new cart
Then the cart creation should proceed normally

Given order "Test" has a cart in state "cancelled"
When the user attempts to create a new cart
Then the cart creation should proceed normally
```

---

### 3. Traceability Matrix

The traceability matrix connects **all** layers:

```
UC → REQ → BR (and vice versa)
```

```markdown
## Complete Matrix: UC → REQ → BR

UC-001-PlaceOrder
├── REQ-001.1 Order Creation
├── REQ-001.2 Title length validation ──────── BR-ORDER-001
├── REQ-001.3 Title required ──────────────── BR-ORDER-001
├── REQ-001.4 Active orders limit ────────── BR-ORDER-002
├── REQ-001.5 Event emission
├── REQ-001.6 AI assistance
└── REQ-001.7 Initial draft status ─────────── BR-ORDER-003
```

#### Uses of the Traceability Matrix

| Use | Description |
|-----|-------------|
| **Development** | When implementing a UC, verify all REQs are covered |
| **Testing** | Generate test cases from REQs, validating BRs |
| **Changes** | When modifying a BR, identify all affected REQs/UCs |
| **Code Review** | Verify that the code implements the referenced BRs |
| **Audit** | Demonstrate compliance with regulatory requirements |

---

### 4. Implementation Mapping

Implementation details (code patterns, schemas, validation libraries) belong in `05-architecture/charter.md`, section "KDD Artifact → Code Mapping". The verification layer stays **technology-agnostic** — it defines WHAT to verify, not HOW to implement it.

When implementing, reference EARS requirements in code comments (e.g., `// REQ-001.2`) to maintain traceability.

---

## Structure of a Requirements Document

### Front-matter

```yaml
---
id: REQ-001
kind: requirement
status: draft
source: UC-001             # Use case it derives from
---
```

### Internal Structure

```markdown
# EARS Requirements: [Use Case Name]

Requirements derived from use case [[UC-NNN-Name]].

## Requirements Summary

| ID | EARS Pattern | Summary |
|----|-------------|---------|
| REQ-NNN.1 | Event-Driven | ... |
| REQ-NNN.2 | Unwanted (If) | ... |

---

## REQ-NNN.1: [Requirement Name] (Pattern)

**Pattern**: [Event-Driven | State-Driven | Unwanted | Optional | Ubiquitous]

[EARS specification in English]

**Traceability**: UC-NNN-Name, [step/extension]; [BR-XXX]

**Acceptance Criterion**:
[Gherkin]

---

## Traceability Matrix

| Requirement | UC Step | Business Rule | Test Case |
|-------------|---------|---------------|-----------|

## Traceability Matrix

[UC → BR → Test Case links]
```

---

## The Complete Verification Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   1. USE CASE (02-Behavior)                                                 │
│      │                                                                       │
│      │ "The Customer creates an Order with title and description"           │
│      │                                                                       │
│      ▼                                                                       │
│   2. DERIVE EARS REQUIREMENTS (04-Verification)                             │
│      │                                                                       │
│      │ "WHEN user submits form, system SHALL create Order"                  │
│      │ "IF title > 100 chars, system SHALL reject"                          │
│      │                                                                       │
│      ▼                                                                       │
│   3. LINK BUSINESS RULES (01-Domain)                                        │
│      │                                                                       │
│      │ REQ-001.2 ← BR-ORDER-001 (title 1-100 chars)                         │
│      │ REQ-001.4 ← BR-ORDER-002 (max 50 active orders)                      │
│      │                                                                       │
│      ▼                                                                       │
│   4. WRITE GHERKIN CRITERIA                                                 │
│      │                                                                       │
│      │ Given user with 50 active orders                                      │
│      │ When user attempts to create new order                               │
│      │ Then error "You have reached the limit" should display               │
│      │                                                                       │
│      ▼                                                                       │
│   5. IMPLEMENT AND TEST                                                      │
│      │                                                                       │
│      │ - Code implements REQ-001.4                                           │
│      │ - Automated test verifies Gherkin criterion                          │
│      │ - Test references REQ and BR in comments                             │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Relationship with Other Layers

The Verification layer **consumes** all previous layers and **is not consumed** by any:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   00-Requirements ──────────────────────────────────────────────┐           │
│       │                                                         │           │
│       │  "Why this requirement?"                                │           │
│       │  → See OBJ: "Success metrics"                           │           │
│       │                                                         ▼           │
│   01-Domain ────────────────────────────────────────┐    04-Verification    │
│       │                                             │           │           │
│       │  "What rule validates this?"                │           │           │
│       │  → BR-ORDER-002: "Maximum 50 active orders" │           │           │
│       │                                             ▼           │           │
│   02-Behavior ──────────────────────────┐     Traceability      │           │
│       │                                 │           │           │           │
│       │  "What command implements it?"  │           │           │           │
│       │  → CMD-001-PlaceOrder           │           │           │           │
│       │  "What use case defines it?"    │           │           │           │
│       │  → UC-001-PlaceOrder            │           │           │           │
│       │                                 ▼           │           │           │
│   03-Experience ─────────────────► UI-Views ───────┘           │           │
│                                       │                         │           │
│                                       │                         │           │
│                                       └───────► REQ-001.1 ──────┘           │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Reference Dependencies

| From | To | Purpose |
|------|----|---------|
| REQ → UC | Origin of the requirement (02-Behavior) |
| REQ → BR | Rule it validates (01-Domain) |
| REQ → CMD | Operation that implements it (02-Behavior) |
| REQ → OBJ | Business justification (00-Requirements) |

---

## Folder Structure

```
/specs/04-verification/
├── criteria/
│   ├── REQ-001-Place-Order.md
│   ├── REQ-002-Configure-Products.md
│   ├── REQ-003-Initiate-Cart.md
│   └── ...
├── contracts/                    # API contracts (optional)
│   └── api-contract.yaml
└── _TRACEABILITY.md             # Global traceability matrix
```

---

## Coverage Metrics

### What to Measure?

```markdown
## Coverage Verification

### Rules Covered by Requirements: 42/44 (95%)

### Requirements Without Associated Rule (Pure UX/Functionality)
- REQ-001.5 (event emission)
- REQ-004.7 (real-time progress)
- REQ-005.1, REQ-005.2 (visualization/navigation)
...

These requirements are UX features that do not violate business rules.
```

### Ideal Coverage

| Metric | Target |
|--------|--------|
| BRs covered by REQs | 100% |
| REQs with Gherkin criteria | 100% |
| UCs with derived REQs | 100% |
| Automated tests per REQ | >= 80% |

---

## Anti-patterns to Avoid

### 1. Vague Requirements

```markdown
# ❌ INCORRECT
The system should validate the input.

# ✅ CORRECT
IF the title exceeds 100 characters,
the system SHALL reject the form submission
  AND SHALL display the error message "Title cannot exceed 100 characters"
  AND SHALL NOT create any Order entity.
```

### 2. Non-Verifiable Criteria

```gherkin
# ❌ INCORRECT
Then the system should work correctly

# ✅ CORRECT
Then a new Order should be created with status "draft"
  And the user should be redirected to /orders/{id}/products
  And a success message "Order created successfully" should be displayed
```

### 3. Broken Traceability

```markdown
# ❌ INCORRECT
REQ-001.4: Order limit
(no reference to UC or BR)

# ✅ CORRECT
REQ-001.4: Unfinished Orders Limit (State-Driven)
**Traceability**: UC-001-PlaceOrder, scenario TC-001.6; BR-ORDER-002
```

### 4. Requirements Without Criteria

```markdown
# ❌ INCORRECT
## REQ-001.1: Order Creation
[Only EARS specification, no Gherkin]

# ✅ CORRECT
## REQ-001.1: Order Creation
[EARS specification]
**Acceptance Criterion**:
[Executable Gherkin]
```

---

## When to Update This Layer

The Verification layer is updated when:

1. **New Use Cases**: Derive REQs for each new UC (from 02-Behavior)
2. **Rule Changes**: Update REQs that reference the modified BR (from 01-Domain)
3. **Bugs Found**: Add Gherkin criteria that capture the bug
4. **Refactoring**: Verify that existing criteria still pass
5. **Audits**: Complete missing traceability

> **Golden rule**: If you write code that has no associated REQ, either the code is unnecessary, or the specification is missing.

---

## Benefits of the Verification Layer

### For Development

- **Clarity**: Know exactly what to implement
- **Pre-written tests**: Gherkin criteria are the basis for automated tests
- **Confidence**: Safe refactoring if tests pass

### For QA

- **Systematic coverage**: No edge cases slip through
- **Clear criteria**: No ambiguity about what to test
- **Automatable regression**: Criteria become tests

### For Product

- **Business traceability**: Every feature linked to Objectives
- **Change impact**: Know what is affected when modifying a rule
- **Living documentation**: Always up to date with the code

### For Audit

- **Compliance**: Demonstrate that regulatory requirements are met
- **History**: Know why each decision was made
- **Measurable coverage**: Objective quality metrics

---

## Summary

The Verification layer in KDD:

1. **Closes the loop**: Connects specifications with verifiable implementation
2. **Uses EARS**: Structured syntax for unambiguous requirements
3. **Includes Gherkin**: Executable acceptance criteria
4. **Maintains traceability**: Complete UC → REQ → BR chain
5. **Enables automation**: Criteria become tests
6. **Is technology-agnostic**: Implementation details belong in 05-architecture

> **"A system without specified verification is a system that only works by accident. KDD ensures it works by design."**

---

## Related Artifacts

- [[requirements.template]] - Template for REQ documents
- [[03-experience]] - Views that consume the use cases
- [[02-behavior]] - Use cases that generate requirements
- [[01-domain]] - Business rules that validate requirements
- [[00-requirements]] - Business objectives
- [[Introduction to KDD]] - KDD overview

---

*Last updated: 2025-01*
