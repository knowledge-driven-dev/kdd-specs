# Layer 00: Requirements

## The Foundation of Everything: Why Does This System Exist?

---

## Introduction

The Requirements layer is the **starting point** of every KDD specification. It answers the most fundamental question: **Why does this system exist?**

Here we do not talk about code, nor technology, nor even software. We talk about **human problems**, about **business context**, about **measurable objectives**. A system that is unclear about its "why" is a system doomed to drift.

> **Important note**: This layer is the **INPUT** that feeds into design. It may mention domain concepts to provide context, but it sits outside the dependency flow between layers.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   00-Requirements  →  01-Domain  →  02-Behavior  →  03-Experience           │
│                                          ↓                                   │
│   "WHY DOES          "What          04-Verification   05-Architecture       │
│    IT EXIST?"         exists?"                                               │
│                                                                              │
│   ──────────────────────────────────────────────────────────────────────────│
│                                                                              │
│   MOTIVATION          Conceptual     Functional       Experiential          │
│   CONTEXT             (entities)     (operations)     (views)               │
│   OBJECTIVES          (rules)        (use cases)      Validation            │
│                       (events)       (processes)      Architecture          │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Philosophy: The Problem Before the Solution

### Why This Layer Comes First

In traditional development, it is common to jump straight into code. "We need an app that does X" → we start coding. This produces systems that:

- Solve problems nobody has
- Do not measure whether they are succeeding
- Accumulate features without clear direction
- Are hard to justify to stakeholders

KDD inverts this:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   TRADITIONAL APPROACH               KDD APPROACH                           │
│   ────────────────────               ────────────                           │
│                                                                              │
│   "Let's build an app"              "What problem are we solving?"          │
│         ↓                                   ↓                               │
│   Start coding                      "For whom?"                             │
│         ↓                                   ↓                               │
│   "What should it do?"              "How do we measure success?"            │
│         ↓                                   ↓                               │
│   Endless features                  Clear and scoped PRD                    │
│         ↓                                   ↓                               │
│   "Was this what they wanted?"      Domain → Capabilities → Experience      │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### The Problem as Anchor

When things get complicated (and they always do), the Requirements layer is your **anchor**:

- "Should we add this feature?" → Does it solve the defined problem?
- "Is this architecture correct?" → Does it meet the business objectives?
- "Do we prioritize X or Y?" → Which one moves us closer to the success metrics?

---

## The Artifacts of the Requirements Layer

### 1. PRD (Product Requirements Document)

The PRD is the central document that defines the product vision.

#### Recommended Structure

```markdown
# PRD: [Product Name]

## The Problem
What pain or need exists? Why is it important to solve it now?

## Target Users
Who are they? What characteristics do they have? How many are there?

## Business Objectives
What do we want to achieve as an organization?

## Success Metrics
How will we know the product is working?

## What This Product Is NOT
Clear boundaries to avoid scope creep.

## Constraints
Technical, legal, time, budget.

## Stakeholders
Who has an interest in this product?
```

#### Example: Store App

```markdown
# PRD: Store App

## The Problem

Online shoppers face friction in the purchasing process where:
- Cart abandonment rates are high due to complex checkout flows
- Inventory availability is unclear until late in the process
- Order tracking is fragmented across multiple systems
- Returns and refunds are cumbersome and slow

A modern e-commerce platform can address these issues,
but existing solutions are:
- Difficult to customize for specific business needs
- Limited by monolithic architecture
- Inconsistent in the quality of the customer experience

## Target Users

**Primary**: Customers who purchase products online
- Individual consumers shopping for everyday goods
- Small business buyers placing bulk orders
- Gift shoppers seeking curated recommendations
- Repeat customers managing subscriptions

**Characteristics**:
- Basic familiarity with online shopping
- Willingness to pay for convenience and quality
- Shop individually or for households/small teams

## Business Objectives

1. Validate that the concept of "Product recommendations" adds value
2. Achieve 1,000 active users in 6 months
3. Reach MRR of $5,000 in 12 months
4. Retention rate > 40% at month 3

## Success Metrics

| Metric | Target | Frequency |
|--------|--------|-----------|
| Completed orders | 5,000/month | Monthly |
| NPS | > 40 | Quarterly |
| Average time to checkout | < 15 min | Weekly |
| % repeat customers | > 60% | Monthly |

## What This Product Is NOT

- Not a replacement for in-person retail
- Not a real-time collaboration tool (v1)
- Not a project management system
- Does not store external documents or files

## Constraints

- **Technical**: Modern stack (Bun, Elysia, Next.js)
- **Legal**: GDPR compliance from day 1
- **Time**: MVP in 3 months
- **Budget**: Bootstrapped, minimal infra costs
```

---

### 2. Objectives (OBJ) - User Objectives

Objectives capture the high-level goals of system actors in User Story format.

#### Structure of an Objective

```yaml
---
id: OBJ-NNN
kind: objective
status: draft
---
```

#### Required Sections

| Section | Required | Description |
|---------|----------|-------------|
| `## Actor` | Yes | Who has this objective |
| `## Objective` | Yes | "As X, I want Y, so that Z" |
| `## Success criteria` | Yes | How the user knows they achieved it |
| `## Related use cases` | No | Links to UC-* |

#### Example

---

### 3. Value Units (UV)

Value Units describe an **end-to-end delivery** that can be used by the user and measured by the business. They are the bridge between objectives and execution, and prevent planning by isolated components.

#### Characteristics

- **Vertical**: they cross domain → behavior → experience → verification.
- **Verifiable**: they have clear exit criteria.
- **Plannable**: they can be estimated and prioritized.

#### Recommended Structure

```yaml
---
id: UV-NNN
kind: value-unit
status: draft
---
```

#### Example

```markdown
# UV-001: Place Order

## Objective
Allow the Customer to place an Order in draft state.

## Scope (end-to-end)
- [[UC-001-PlaceOrder]]
- [[CMD-001-PlaceOrder]]
- [[REQ-001-PlaceOrder]]
- [[UI-OrderEditor]]

## Exit criteria
- REQ-001 validated with feature.
- UI-OrderEditor in storybook.
- Command implemented and tested.
```

---

### 4. Release Plans (REL)

Release Plans group Value Units into deliveries (MVP, v1, v1.1) with objectives, dependencies, and exit criteria. They are the primary planning instrument.

#### Recommended Structure

```yaml
---
id: REL-NNN
kind: release
status: draft
---
```

#### Example

```markdown
# REL-001: MVP

## Objective
Validate Order creation and Product configuration.

## Value Units
- [[UV-001-PlaceOrder]]
- [[UV-002-ConfigureProducts]]

## Dependencies
- [[ADR-0002-Elysia-Backend]]

## Exit criteria
- 100% UV with full traceability.
- Critical tests passing.
```

```markdown
---
id: OBJ-001
kind: objective
status: approved
---

# OBJ-001: Browse and purchase products efficiently

## Actor
[[Customer]] - Individual who purchases products online

## Objective
As a Customer, I want to browse products and place orders seamlessly,
so that I can get what I need without friction or delays.

## Success criteria
- I can create an Order with the products I want to purchase
- I receive confirmation and tracking from the fulfillment process
- I get a clear summary that synthesizes my order details

## Related use cases
- [[UC-001-PlaceOrder]]
- [[UC-003-StartCart]]
- [[UC-005-ViewOrderSummary]]
```

---

### 3. Business Context

A document that captures the environment in which the system operates.

#### What It Includes

```markdown
# Business Context

## Current Situation
How is the problem solved today? What tools exist?

## Competition
Who else is in this space? How do we differentiate?

## Market Trends
What changes are happening that make this relevant?

## Related Systems
What other systems must it integrate or coexist with?

## Business Model
How do we generate revenue? What is the cost structure?
```

---

### 4. ADRs (Architecture Decision Records)

ADRs document important decisions and their justification. All ADRs live in `05-architecture/decisions/`. Business/strategy ADRs and purely technical ADRs coexist in the same location.

#### Structure of an ADR

```yaml
---
id: ADR-NNNN
kind: adr
status: draft            # draft|review|approved|deprecated|superseded
supersedes: []
superseded_by:
---
```

#### Example

```markdown
---
id: ADR-0001
kind: adr
status: approved
---

# ADR-0001: Credit-based monetization model

## Context
We need a monetization model for Store App that is
fair for occasional users and scalable for power users.

## Options Considered

### Option A: Fixed monthly subscription
- **Pros**: Predictable revenue, simple to implement
- **Cons**: High barrier for occasional users

### Option B: Pay per order
- **Pros**: Pay only for what you use
- **Cons**: Friction on each use, unpredictable

### Option C: Credit system
- **Pros**: Flexibility, allows packages, reduces friction
- **Cons**: More complex to implement

## Decision
We chose **Credit system** because:
1. It allows users to try with a few initial credits
2. Power users can buy packages at a discount
3. It facilitates promotions and onboarding

## Consequences

### Positive
- Better conversion from free to paid users
- Flexibility in future pricing

### Negative
- Additional system complexity
- Need to clearly communicate the value of a credit

## References
- Benchmark of similar apps (Notion, Linear)
```

#### When to Create an ADR

- Choice of business model
- Market or positioning decision
- Product strategy change
- Any decision that someone will ask "why did we do this?"

---

## The Ubiquitous Language Is Born Here

Although domain terms are formally defined in layer 01-Domain, **they begin to emerge in the PRD**.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   In the PRD we mention:                                                     │
│   - "Shopping carts"                                                         │
│   - "Products"                                                               │
│   - "Categories" (product types)                                             │
│   - "Orders" (purchases to fulfill)                                          │
│                                                                              │
│   These terms are formalized in 01-Domain as Entities:                       │
│   - [[Cart]]                                                                 │
│   - [[Product]]                                                              │
│   - [[Category]]                                                             │
│   - [[Order]]                                                                │
│                                                                              │
│   The PRD is where the shared vocabulary is BORN.                            │
│   The Domain is where it is formally DEFINED.                                │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Relationship with Other Layers

The Requirements layer is the **INPUT** that feeds into design. It sits **outside the dependency flow**:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   00-Requirements (INPUT - outside the layer flow)                           │
│       │                                                                      │
│       │  feeds into design                                                   │
│       ▼                                                                      │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  04-Verification   (tests, criteria)                                │   │
│   │      ↓ references                                                   │   │
│   ├─────────────────────────────────────────────────────────────────────┤   │
│   │  03-Experience     (views, flows, components)                       │   │
│   │      ↓ references                                                   │   │
│   ├─────────────────────────────────────────────────────────────────────┤   │
│   │  02-Behavior       (UC, CMD, QRY, XP, processes)                    │   │
│   │      ↓ references                                                   │   │
│   ├─────────────────────────────────────────────────────────────────────┤   │
│   │  01-Domain         (entities, events, rules)   ← BASE               │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│   05-Architecture (ORTHOGONAL - ADRs, Implementation Charter)                │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Folder Structure

```
/specs/00-requirements/
├── PRD.md                    # Main product document
├── context.md                # Business context (optional)
├── /objectives/
│   ├── OBJ-001-StructuredAnalysis.md
│   └── OBJ-002-ProductRecommendations.md
├── /value-units/
│   └── UV-001-PlaceOrder.md
└── /releases/
    └── REL-001-MVP.md
```

> **Note**: ADRs are in `05-architecture/decisions/`, not in this layer.

---

## Checklist: Defining the Problem

Before moving on to other layers, make sure you have:

- [ ] **Clear problem**: What pain are we solving? Why now?
- [ ] **Defined users**: Who are they? What characteristics do they have?
- [ ] **Business objectives**: What do we want to achieve as an organization?
- [ ] **Success metrics**: How do we measure if it works?
- [ ] **Clear boundaries**: What is this product NOT?
- [ ] **Constraints**: What limitations do we have?
- [ ] **Objectives (OBJ)**: What do users want to achieve?
- [ ] **Initial ADRs**: What business decisions have we made? (recorded in `05-architecture/decisions/`)

---

## Anti-patterns to Avoid

### 1. PRD as a Feature List

```markdown
# INCORRECT
## Features
- Login with Google
- Dashboard with charts
- Export to PDF
- Dark mode

# CORRECT
## The Problem
Teams make biased decisions because...

## Success Metrics
- NPS > 40
- Analysis time < 15 min
```

### 2. Vague Objectives

```markdown
# INCORRECT
## Objectives
- Make a useful app
- Get people to use it
- Make money

# CORRECT
## Objectives
- 1,000 active users in 6 months
- MRR of $5,000 in 12 months
- Retention > 40% at month 3
```

### 3. Missing "What It Is NOT"

```markdown
# INCORRECT
(No boundaries section)

# CORRECT
## What This Product Is NOT
- Not a replacement for in-person retail
- Not real-time collaboration (v1)
- Does not manage projects
```

### 4. Undocumented Decisions

```markdown
# INCORRECT
"We use credits just because"

# CORRECT
ADR-0001 with:
- Context
- Options considered
- Pros/cons of each
- Decision and justification
- Consequences
```

---

## When to Update This Layer

The Requirements layer **changes infrequently** once established. Update it when:

1. **Product pivot**: The problem or users change fundamentally
2. **New objectives**: The organization's OKRs evolve
3. **Strategic decisions**: New ADRs due to business changes
4. **Post-mortem**: Learnings that change the vision

> **Golden rule**: If you change the PRD frequently, you probably did not have the problem clear from the start.

---

## Summary

The Requirements layer in KDD:

1. **Is the foundation**: Everything else derives from here
2. **Is stable**: Changes infrequently once defined
3. **Is the "why"**: Justifies every subsequent decision
4. **Is measurable**: Defines concrete success metrics
5. **Is limiting**: Establishes what the product is NOT
6. **Documents decisions**: ADRs capture the reasoning
7. **Captures objectives**: OBJ defines what users want to achieve

> **"A system without a clear problem is a solution looking for a problem. KDD starts with the problem."**

---

## Related Artifacts

- [[prd.template]] - Template for PRD
- [[adr.template]] - Template for ADRs
- [[objective.template]] - Template for Objectives
- [[01-domain]] - The next layer: Domain
- [[Introduction to KDD]] - KDD overview

---

*Last updated: 2025-01*
