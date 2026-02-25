# KDD Layer Structure

## Overview

KDD specifications are organized in 6 layers. Each layer has a clear responsibility and dependency rules.

```
/specs
├── _kdd.yaml              # Meta: KDD version
├── 00-requirements/       # WHY — Business context
├── 01-domain/             # WHAT EXISTS — Core model
├── 02-behavior/           # HOW IT WORKS — System behavior
├── 03-experience/         # HOW USERS SEE IT — User interaction
├── 04-verification/       # HOW WE TEST IT — Validation
└── 05-architecture/       # HOW WE BUILD IT — Technical decisions
```

---

## Layer Details

### 00-requirements — Business Context

**Purpose**: Capture *why* the system exists and what outcomes it should achieve.

```
00-requirements/
├── PRD.md                    # Product Requirements Document (high-level)
├── objectives/               # OBJ-NNN-{Name}.md
├── value-units/              # UV-NNN-{Name}.md (end-to-end deliverables)
└── releases/                 # REL-NNN-{Name}.md
```

**Key point**: This layer is *input* to design. It is intentionally outside the strict 01→04 dependency chain — it can reference domain concepts for context without violating layer rules.

---

### 01-domain — Domain Model

**Purpose**: Define *what exists* in the system. The foundational layer.

```
01-domain/
├── entities/                 # PascalCase.md (entities, roles, external systems)
├── events/                   # EVT-{Entity}-{Action}.md
└── rules/                    # BR-NNN-{Name}.md (business rules)
```

**Dependency rule**: Should NOT reference higher layers (02, 03, 04).

---

### 02-behavior — System Behavior

**Purpose**: Define *how the system works*. Orchestrates domain objects.

```
02-behavior/
├── commands/                 # CMD-NNN-{Name}.md (state-changing operations)
├── queries/                  # QRY-NNN-{Name}.md (read operations)
├── processes/                # PROC-NNN-{Name}.md (multi-step workflows)
├── policies/                 # BP-NNN-{Name}.md, XP-NNN-{Name}.md
└── use-cases/                # UC-NNN-{Name}.md (actor↔system interactions)
```

**Dependency rule**: CAN reference 01-domain. Should NOT reference 03 or 04.

---

### 03-experience — User Interaction

**Purpose**: Define *how users see and interact* with the system.

```
03-experience/
├── views/                    # UI-{Name}.md (pages/screens)
├── flows/                    # Navigation flows between views
└── components/               # Reusable UI components
```

**Dependency rule**: CAN reference 01-domain and 02-behavior. Should NOT reference 04.

---

### 04-verification — Validation

**Purpose**: Define *how we know the system works correctly*.

```
04-verification/
├── criteria/                 # REQ-NNN-{Name}.md (acceptance criteria)
└── examples/                 # *.feature (BDD/Gherkin scenarios)
```

**Dependency rule**: CAN reference any layer (01, 02, 03).

---

### 05-architecture — Technical Decisions

**Purpose**: Record *how we build it* — technology choices, patterns, and implementation guidelines.

```
05-architecture/
├── decisions/                # ADR-NNNN-{Title}.md (architecture decisions)
└── charter.md                # Implementation Charter (stack, conventions, mappings)
```

**Dependency rule**: This layer is *orthogonal* — like 00-requirements, it does not participate in the 01→04 dependency chain. It can reference any layer for context. Implementation hints from other artifacts (code patterns, schemas, conventions) belong here, in the charter's "KDD Artifact → Code Mapping" section.

---

## Dependency Rules (Summary)

```
┌───────────────────────────────────────────────────────────────┐
│  00-requirements   (PRD, objectives, value-units, releases)   │
│  INPUT: Feeds design. May mention domain concepts for          │
│  context. Not part of the layer dependency flow.               │
└───────────────────────────────────────────────────────────────┘
                              ↓ feeds
┌───────────────────────────────────────────────────────────────┐
│  04-verification   (REQ, BDD scenarios)                       │
│      ↓ references                                             │
├───────────────────────────────────────────────────────────────┤
│  03-experience     (views, flows, components)                 │
│      ↓ references                                             │
├───────────────────────────────────────────────────────────────┤
│  02-behavior       (UC, CMD, QRY, XP, processes)              │
│      ↓ references                                             │
├───────────────────────────────────────────────────────────────┤
│  01-domain         (entities, events, rules)   ← BASE         │
└───────────────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────────────┐
│  05-architecture   (ADR, Implementation Charter)              │
│  ORTHOGONAL: Records technical decisions and implementation   │
│  guidelines. Does not participate in the 01→04 chain.         │
└───────────────────────────────────────────────────────────────┘
```

Higher layers (04→03→02→01) CAN reference lower layers.
Lower layers SHOULD NOT reference higher layers.

---

## Multi-Domain Structure (Optional)

For large applications with multiple bounded contexts:

```
/specs
├── _shared/                  # Cross-domain policies and glossary
│   ├── policies/             # XP-* policies
│   └── domain-map.md
├── domains/
│   ├── core/                 # Foundational domain
│   │   ├── _manifest.yaml    # Domain metadata
│   │   ├── 01-domain/
│   │   └── ...
│   ├── auth/
│   └── billing/
└── _index.json               # Global index
```

**Cross-domain references**: Use `[[domain::Entity]]` syntax, e.g., `[[core::Customer]]`.

---

## Naming Conventions

| Artifact | File Pattern | Example |
|----------|-------------|---------|
| Entity | `PascalCase.md` | `Order.md`, `Customer.md` |
| Role | `PascalCase.md` | `Admin.md` |
| External System | `UPPERCASE.md` | `STRIPE.md` |
| Event | `EVT-{Entity}-{Action}.md` | `EVT-Order-Placed.md` |
| Business Rule | `BR-NNN-{Name}.md` | `BR-001-MinOrderAmount.md` |
| Command | `CMD-NNN-{Name}.md` | `CMD-001-PlaceOrder.md` |
| Query | `QRY-NNN-{Name}.md` | `QRY-001-GetOrder.md` |
| Process | `PROC-NNN-{Name}.md` | `PROC-001-Onboarding.md` |
| Use Case | `UC-NNN-{Name}.md` | `UC-001-PlaceOrder.md` |
| ADR | `ADR-NNNN-{Title}.md` | `ADR-0001-Use-PostgreSQL.md` |
| Impl Charter | `charter.md` | `charter.md` |
