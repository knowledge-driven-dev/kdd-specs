# KDD Model Reference (Agent Context)

> **Purpose**: Minimal context for subagents working with KDD specifications.
> For human documentation, see the `docs/` directory in this repo.

## Core Principle

**Specification > Code**: Docs are the source of truth; code is derived/regenerable.

---

## Folder Structure (`/specs`)

```
/specs
├── 00-requirements/       # INPUT - Business context (feeds design)
│   ├── PRD.md
│   ├── objectives/        # OBJ-NNN-*.md (High-level User Stories)
│   ├── value-units/       # UV-NNN-*.md (Value Units)
│   ├── releases/          # REL-NNN-*.md (Release plans)
│   └── decisions/         # ADR-NNN-*.md
├── 01-domain/             # BASE - Domain model (foundation)
│   ├── entities/          # Entity.md (PascalCase)
│   ├── events/            # EVT-Entity-Action.md
│   └── rules/             # BR-*-NNN.md (Business Rules)
├── 02-behavior/           # ORCHESTRATION - How the system behaves
│   ├── commands/          # CMD-NNN-*.md
│   ├── queries/           # QRY-NNN-*.md
│   ├── processes/         # PROC-NNN-*.md
│   ├── policies/          # BP-*-NNN.md, XP-*-NNN.md (Business & Cross Policies)
│   └── use-cases/         # UC-NNN-*.md
├── 03-experience/         # PRESENTATION - How users see it
│   └── views/             # UI-*.md
└── 04-verification/       # VALIDATION - How we test it
    ├── criteria/          # REQ-NNN-*.md
    └── examples/          # *.feature (BDD)
```

> **Note**: Architecture decisions (ADRs) live in `00-requirements/decisions/`, not in a separate `05-architecture/` layer.


### Multi-Domain Structure (Optional)

For large applications with multiple bounded contexts, use the multi-domain structure:

```
/specs
├── _shared/                  # Cross-domain policies and glossary
│   ├── policies/             # XP-* policies
│   └── domain-map.md         # Domain dependency visualization
├── domains/
│   ├── core/                 # Foundational domain
│   │   ├── _manifest.yaml    # Domain metadata and dependencies
│   │   ├── 01-domain/
│   │   └── ...
│   ├── auth/                 # Authentication domain
│   └── sessions/             # Sessions domain
└── _index.json               # Global index
```

**Cross-domain references**: Use `[[domain::Entity]]` syntax (e.g., `[[core::Customer]]`).

**See**: `/kdd/docs/multi-domain.md` for complete documentation.

---

## Artifact Types & IDs

| Type            | Prefix | ID Pattern              | File Pattern               | Location                       |
| --------------- | ------ | ----------------------- | -------------------------- | ------------------------------ |
| Objective       | OBJ    | `OBJ-NNN`               | `OBJ-NNN-{Name}.md`        | `00-requirements/objectives/`  |
| Value Unit      | UV     | `UV-NNN`                | `UV-NNN-{Name}.md`         | `00-requirements/value-units/` |
| Release         | REL    | `REL-NNN`               | `REL-NNN-{Name}.md`        | `00-requirements/releases/`    |
| ADR             | ADR    | `ADR-NNNN`              | `ADR-NNNN-{Title}.md`      | `00-requirements/decisions/`   |
| Entity          | -      | -                       | `PascalCase.md`            | `01-domain/entities/`          |
| Event           | EVT    | `EVT-{Entity}-{Action}` | `EVT-{Entity}-{Action}.md` | `01-domain/events/`            |
| Business Rule   | BR     | `BR-{ENTITY}-NNN`       | `BR-{ENTITY}-NNN.md`       | `01-domain/rules/`             |
| Business Policy | BP     | `BP-{TOPIC}-NNN`        | `BP-{TOPIC}-NNN.md`        | `02-behavior/policies/`        |
| Command         | CMD    | `CMD-NNN`               | `CMD-NNN-{Name}.md`        | `02-behavior/commands/`        |
| Query           | QRY    | `QRY-NNN`               | `QRY-NNN-{Name}.md`        | `02-behavior/queries/`         |
| Process         | PROC   | `PROC-NNN`              | `PROC-NNN-{Name}.md`       | `02-behavior/processes/`       |
| Use Case        | UC     | `UC-NNN`                | `UC-NNN-{Name}.md`         | `02-behavior/use-cases/`       |
| Cross-Policy    | XP     | `XP-{TOPIC}-NNN`        | `XP-{TOPIC}-NNN.md`        | `02-behavior/policies/`        |
| UI View         | UI     | -                       | `UI-{Name}.md`             | `03-experience/views/`         |
| Requirement     | REQ    | `REQ-NNN`               | `REQ-NNN-{Name}.md`        | `04-verification/criteria/`    |

---

## Layer Dependencies

```
┌───────────────────────────────────────────────────────────────┐
│  00-requirements   (PRD, objectives, ADRs)                    │
│  INPUT: Feeds design. May mention domain concepts for          │
│  context. Not part of the layer dependency flow.               │
└───────────────────────────────────────────────────────────────┘
                              ↓ feeds
┌───────────────────────────────────────────────────────────────┐
│  04-verification   (tests, criteria)                          │
│      ↓ references                                             │
├───────────────────────────────────────────────────────────────┤
│  03-experience     (views)                                    │
│      ↓ references                                             │
├───────────────────────────────────────────────────────────────┤
│  02-behavior       (UC, CMD, QRY, XP)                         │
│      ↓ references                                             │
├───────────────────────────────────────────────────────────────┤
│  01-domain         (entities, rules)   ← BASE                 │
└───────────────────────────────────────────────────────────────┘
```

**Rule**: Higher layers (04→03→02→01) CAN reference lower layers. Lower layers SHOULD NOT reference higher layers.

> **00-requirements** is outside the dependency flow. It is the input that feeds design, so it can naturally mention domain concepts without violating the layer rule.

---

## Status Lifecycle

All artifacts use the same cycle:

```
draft → review → approved → deprecated
```

| Status | Meaning |
|--------|---------|
| `draft` | Work in progress, not yet source of truth |
| `review` | Pending approval |
| `approved` | Official source of truth |
| `deprecated` | Obsolete, should link to replacement |

> **Note**: Do not use `proposed`. Use `review` for artifacts pending approval.

---

## Language Policy

| Aspect                     | Language | Example                                 |
| -------------------------- | -------- | --------------------------------------- |
| Section headings           | English  | `## Description`, `## Main Flow`        |
| Template instructions      | English  | `<!-- 1-3 lines: what this view does -->` |
| Code and examples          | English  | `function placeOrder()`                 |
| IDs and prefixes           | English  | `CMD-009`, `BR-ORDER-001`               |
| Narrative content (specs)  | Any      | Users write spec content in their language |

> **Note**: Section headings, template instructions, and structural content must be in English. The narrative content that users write inside their specs (descriptions, examples, etc.) can be in any language.

---

## Front-Matter by Type

> **Note**: The `kind` attribute indicates the KDD artifact type. `tags` are optional and used for additional categorization (not for indicating the type).

### Entity

```yaml
---
id: "{EntityName}"       # Optional, defaults to filename
kind: entity             # Required
aliases: []              # Optional alternative names
status: draft            # draft|review|approved|deprecated
---
```

### Event (EVT)

```yaml
---
id: EVT-{Entity}-{Action}  # Required, must match filename
kind: event                # Required
title: "{Entity} {Action}" # Required
status: draft
---
```

### Business Rule (BR)

```yaml
---
id: BR-{ENTITY}-NNN      # Required, e.g., BR-CART-001
kind: business-rule      # Required
title: RuleName          # Required
entity: EntityName       # Required: primary affected entity
category: validation     # validation|state|limit|security
severity: medium         # low|medium|high|critical
status: draft
---
```

### Business Policy (BP)

```yaml
---
id: BP-{TOPIC}-NNN       # Required, e.g., BP-BILLING-001
kind: business-policy    # Required
title: PolicyName        # Required
entity: EntityName       # Optional: primary affected entity
category: business       # business|compliance|limit
severity: medium         # low|medium|high|critical
status: draft
---
```

### Cross-Policy (XP)

```yaml
---
id: XP-{TOPIC}-NNN       # Required, e.g., XP-BILLING-001
kind: cross-policy       # Required
title: PolicyName        # Required
status: draft
---
```

### Command (CMD)

```yaml
---
id: CMD-NNN              # Required, pattern: ^CMD-\d{3}$
kind: command            # Required
title: CommandName       # Required
status: draft
billable: false          # Optional: if true, applies XP-BILLING-001
credit-cost: 0           # Optional: credits consumed (requires billable: true)
tags: [core, destructive] # Optional: categorization tags
---
```

### Query (QRY)

```yaml
---
id: QRY-NNN              # Required, pattern: ^QRY-\d{3}$
kind: query              # Required
title: QueryName         # Required
status: draft
---
```

### Use Case (UC)

```yaml
---
id: UC-NNN               # Required, pattern: ^UC-\d{3}$
kind: use-case           # Required
title: UseCaseName       # Required
version: 1               # Number, default: 1
status: draft            # draft|review|approved|deprecated
actor: ActorName         # Required
---
```

### UI View

```yaml
---
id: UI-{Name}            # Optional, defaults to filename
kind: ui-view            # Required
title: ViewName          # Required
status: draft
links:
  use-cases: []          # UCs this view triggers
  components: []         # UI components used
---
```

### UI Component

```yaml
---
id: UI-{Name}            # Optional, defaults to filename
kind: ui-component       # Required
title: ComponentName     # Required
status: draft
links:
  entities: []           # Domain entities used
  use-cases: []          # UCs this component supports
---
```

### Requirement (REQ)

```yaml
---
id: REQ-NNN              # Required, pattern: ^REQ-\d{3}$
kind: requirement        # Required
title: RequirementName   # Required
status: draft
priority: medium         # low|medium|high|critical
source: PRD              # Where this requirement comes from
---
```

### Objective (OBJ)

```yaml
---
id: OBJ-NNN              # Required, pattern: ^OBJ-\d{3}$
kind: objective          # Required
title: ObjectiveName     # Required
actor: ActorName         # Required
status: draft
---
```

### Process (PROC)

```yaml
---
id: PROC-NNN             # Required, pattern: ^PROC-\d{3}$
kind: process            # Required
title: ProcessName       # Required
status: draft
---
```

---

## Required Sections by Type

### Entity

| Section | Required | Description |
|---------|----------|-------------|
| `## Description` | Yes | What it is and what it's for |
| `## Attributes` | Yes | Table with fields |
| `## Lifecycle` | No | Mermaid stateDiagram |
| `## Relations` | No | Relationships with other entities |
| `## Invariants` | No | Constraints that must always hold |

### Command

| Section | Required | Description |
|---------|----------|-------------|
| `## Purpose` | Yes | What the command does |
| `## Input` | Yes | Table: Parameter, Type, Required, Validation |
| `## Preconditions` | Yes | List of prior conditions |
| `## Postconditions` | Yes | State after execution |
| `## Possible Errors` | Yes | Table: Code, Condition, Message |

**Required columns for Input:**
| Parameter | Type | Required | Validation |
|-----------|------|----------|------------|

**Required columns for Errors:**
| Code | Condition | Message |
|------|-----------|---------|

### Query

| Section | Required | Description |
|---------|----------|-------------|
| `## Purpose` | Yes | What data it returns |
| `## Input` | Yes | Table with parameters |
| `## Output` | Yes | Response structure |
| `## Possible Errors` | Yes | Error table |

### Use Case

| Section | Required | Description |
|---------|----------|-------------|
| `## Description` | Yes | Use case summary |
| `## Actors` | Yes | Who participates |
| `## Preconditions` | Yes | Required initial state |
| `## Main Flow` | Yes | Happy path steps |
| `## Alternative Flows` | No | Valid variations |
| `## Exceptions` | No | What happens when something fails |
| `## Postconditions` | Yes | Final state |
| `## Applied Rules` | No | BR-*, BP-*, XP-* that are validated |
| `## Executed Commands` | No | CMD-* that are invoked |

### Business Rule (BR/BP)

| Section | Required | Description |
|---------|----------|-------------|
| `## Statement` | Yes | Clear description with wiki-links |
| `## Why It Exists` | Yes | Business justification |
| `## When It Applies` | Yes | Activation conditions |
| `## What Happens on Violation` | Yes | Consequences |
| `## Parameters` | BP only | Configurable values |
| `## Formalization` | No | EARS pattern |
| `## Examples` | Yes | Valid and invalid cases |

### Cross-Policy (XP)

| Section | Required | Description |
|---------|----------|-------------|
| `## Purpose` | Yes | Why it is cross-cutting |
| `## Statement` | Yes | What the policy does |
| `## EARS Formalization` | Yes | BEFORE/AFTER pattern |
| `## Examples` | Yes | Successful/failed verification |
| `## Standard Behavior` | Yes | BEFORE, AFTER, Rejection, Rollback |
| `## Implementation` | No | Reference code |

### UI View

| Section | Required | Description |
|---------|----------|-------------|
| `## Purpose` | Yes | Purpose of the view |
| `## Layout` | Yes | ASCII wireframe or image |
| `## Components` | Yes | List of components used |
| `## States` | Yes* | loading, empty, error, success |
| `## Behavior` | Yes | Interactions and navigation |

> *States: Include only those **applicable** to the view. A view with no data can omit `empty`. A static view can omit `loading`.

### Requirement (REQ)

| Section | Required | Description |
|---------|----------|-------------|
| `## Description` | Yes | What must be fulfilled |
| `## Acceptance Criteria` | Yes | List of verifiable conditions |
| `## Traceability` | Yes | Links to UC-*, BR-*, CMD-* that implement it |

### Objective (OBJ)

| Section | Required | Description |
|---------|----------|-------------|
| `## Actor` | Yes | Who has this objective |
| `## Objective` | Yes | "As X, I want Y, so that Z" |
| `## Success Criteria` | Yes | How the user knows they achieved it |
| `## Related Use Cases` | No | Links to UC-* |

---

## Wiki-Link Syntax

```markdown
[[Order]]                      # Link to entity
[[Product|products]]           # Link with display alias
[[BR-ORDER-001]]               # Link to rule
[[CMD-001-PlaceOrder]]         # Link to command
[[UC-001-PlaceOrder]]          # Link to use case
[[XP-BILLING-001]]             # Link to cross-policy
```

---

## Naming Conventions

### Domain Entities in Text

- **Always capitalize** domain entities: `The Customer places an Order`
- First mention → wiki-link: `[[Order]]`
- Plurals with alias: `[[Product|Products]]`
- In code → lowercase: `const order = await placeOrder()`

### File Names

- Entities: `PascalCase.md` (e.g., `Order.md`, `CartItem.md`)
- Everything else: Use prefix pattern (e.g., `CMD-001-PlaceOrder.md`)

---

## Templates

All templates are in `templates/` (in the kdd-specs repo):

| Template | For |
|----------|-----|
| `entity.template.md` | Domain entities, roles, external systems |
| `event.template.md` | Domain events |
| `rule.template.md` | Business rules (BR) |
| `command.template.md` | Commands (CQRS write) |
| `query.template.md` | Queries (CQRS read) |
| `process.template.md` | Business processes |
| `use-case.template.md` | Use cases |
| `ui-view.template.md` | UI views/pages |
| `ui-flow.template.md` | UI navigation flows |
| `ui-component.template.md` | Reusable UI components |
| `prd.template.md` | Product Requirements Document |
| `adr.template.md` | Architecture Decision Record |
| `requirement.template.md` | Functional requirements |
| `nfr.template.md` | Non-functional requirements |

---

## Validation

Use `kdd-tools` validator:

```bash
# From your project directory
bunx kdd-validate ./specs
```

Checks: front-matter schema, broken links, required sections.

---

## Key References

- Layer structure: `structure/layers.md`
- Human docs: `docs/` directory
- Writing conventions: `docs/writing-conventions.md`
- Multi-domain support: `docs/multi-domain.md`
