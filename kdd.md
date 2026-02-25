# KDD Model Reference (Agent Context)

> **Purpose**: Minimal context for subagents working with KDD specifications.
> For human documentation, see the `docs/` directory in this repo.

## Core Principle

**Specification > Code**: Docs are the source of truth; code is derived/regenerable.

---

## Folder Structure (`/specs`)

```
/specs
├── _kdd.yaml              # Meta: KDD version (currently "2.0")
├── 00-requirements/       # INPUT - Business context (feeds design)
│   ├── PRD.md
│   ├── objectives/        # OBJ-NNN-*.md (High-level User Stories)
│   ├── value-units/       # UV-NNN-*.md (Value Units)
│   └── releases/          # REL-NNN-*.md (Release plans)
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
│   ├── views/             # UI-*.md
│   ├── flows/             # Navigation flows
│   └── components/        # Reusable UI components
├── 04-verification/       # VALIDATION - How we test it
│   ├── criteria/          # REQ-NNN-*.md
│   └── examples/          # *.feature (BDD)
└── 05-architecture/       # TECHNICAL - How we build it (orthogonal)
    ├── decisions/         # ADR-NNNN-*.md
    └── charter.md         # Implementation Charter
```

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
| UI Flow         | -      | -                       | `{FlowName}.md`            | `03-experience/flows/`         |
| UI Component    | -      | -                       | `{ComponentName}.md`       | `03-experience/components/`    |
| Requirement     | REQ    | `REQ-NNN`               | `REQ-NNN-{Name}.md`        | `04-verification/criteria/`    |
| NFR             | NFR    | `NFR-{Name}`            | `NFR-{Name}.md`            | `00-requirements/`             |
| ADR             | ADR    | `ADR-NNNN`              | `ADR-NNNN-{Title}.md`      | `05-architecture/decisions/`   |
| Impl Charter    | -      | `ARCH-CHARTER-*`        | `charter.md`               | `05-architecture/`             |

---

## Layer Dependencies

```
┌───────────────────────────────────────────────────────────────┐
│  00-requirements   (PRD, objectives, value-units, releases)   │
│  INPUT: Feeds design. May mention domain concepts for          │
│  context. Not part of the layer dependency flow.               │
└───────────────────────────────────────────────────────────────┘
                              ↓ feeds
┌───────────────────────────────────────────────────────────────┐
│  04-verification   (tests, criteria)                          │
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

**Rule**: Higher layers (04→03→02→01) CAN reference lower layers. Lower layers SHOULD NOT reference higher layers.

> **00-requirements** is outside the dependency flow. It is the input that feeds design, so it can naturally mention domain concepts without violating the layer rule.

> **05-architecture** is orthogonal. It records HOW we build (technology, patterns, conventions) but does not participate in the WHAT chain (01→04). Implementation hints from other artifacts belong here, in the charter's "KDD Artifact → Code Mapping" section.

---

## Status Lifecycle

All artifacts use the same cycle:

```
draft → review → approved → deprecated
                         ↘ superseded
```

| Status | Meaning |
|--------|---------|
| `draft` | Work in progress, not yet source of truth |
| `review` | Pending approval |
| `approved` | Official source of truth |
| `deprecated` | Obsolete, should link to replacement |
| `superseded` | Replaced by another version (used by ADRs, Charters) |

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

All artifacts share a **universal base** of 3 attributes:

```yaml
id: TYPE-NNN     # @required (except entity, which uses filename)
kind: <type>     # @required
status: draft    # @required @enum: draft|review|approved|deprecated|superseded
```

Only type-specific extras are added where strictly necessary. No other attributes (`title`, `owner`, `tags`, `version`, `domain`, `created`, `author`, `billable`, etc.) belong in front-matter.

### Entity

```yaml
---
kind: entity             # @required @enum: entity|role|system
aliases: []              # Alternative names for search/indexing
---
```

> Entity uses filename as identity (PascalCase.md). No `id` field.

### Event (EVT)

```yaml
---
kind: event              # @required
---
```

> Event uses title as identity (EVT-Entity-Action). No `id` field.

### Business Rule (BR) / Business Policy (BP)

```yaml
---
id: BR-ENTITY-NNN       # @required @pattern: ^(BR|BP)-[A-Z]+-\d{3}$
kind: business-rule      # @required @enum: business-rule|business-policy
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### Cross-Policy (XP)

```yaml
---
id: XP-{TOPIC}-NNN      # @required @pattern: ^XP-[A-Z]+-\d{3}$
kind: cross-policy       # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### Command (CMD)

```yaml
---
id: CMD-NNN              # @required @pattern: ^CMD-\d{3}$
kind: command            # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### Query (QRY)

```yaml
---
id: QRY-NNN              # @required @pattern: ^QRY-\d{3}$
kind: query              # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### Use Case (UC)

```yaml
---
id: UC-NNN               # @required @pattern: ^UC-\d{3}$
kind: use-case           # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### Process (PROC)

```yaml
---
id: PROC-NNN             # @required @pattern: ^PROC-\d{3}$
kind: process            # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### UI View / UI Component / UI Flow

```yaml
---
kind: ui-view            # @required (or ui-component, ui-flow)
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### PRD

```yaml
---
id: PRD-Name             # @required @pattern: ^PRD-.+
kind: prd                # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### NFR

```yaml
---
id: NFR-Name             # @required @pattern: ^NFR-.+
kind: nfr                # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### Requirement (REQ)

```yaml
---
id: REQ-NNN              # @required @pattern: ^REQ-\d{3}$
kind: requirement        # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
source: UC-NNN           # @pattern: ^UC-\d{3}$ — traceability to source use case
---
```

### ADR

```yaml
---
id: ADR-NNNN             # @required @pattern: ^ADR-\d{4}$
kind: adr                # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
supersedes: []           # ADRs this one replaces
superseded_by:           # ADR that replaces this one
---
```

### Implementation Charter

```yaml
---
id: ARCH-CHARTER-XXXX    # @required @pattern: ^ARCH-CHARTER-[A-Z0-9\-]+$
kind: implementation-charter # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
supersedes: []           # Previous charter versions
---
```

### Value Unit (UV)

```yaml
---
id: UV-NNN               # @required @pattern: ^UV-\d{3}$
kind: value-unit         # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### Release (REL)

```yaml
---
id: REL-NNN              # @required @pattern: ^REL-\d{3}$
kind: release            # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

### Objective (OBJ)

```yaml
---
id: OBJ-NNN              # @required @pattern: ^OBJ-\d{3}$
kind: objective          # @required
status: draft            # @required @enum: draft|review|approved|deprecated|superseded
---
```

---

## Required Sections by Type

### Entity

| Section | Required | Description |
|---------|----------|-------------|
| `## Description` | Yes | What it is and what it's for |
| `## Attributes` | Yes | Table with fields |
| `## States` | If has lifecycle | State table and transitions |
| `## Lifecycle` | No | Mermaid stateDiagram |
| `## Invariants` | No | Constraints that must always hold |

### Command

| Section | Required | Description |
|---------|----------|-------------|
| `## Purpose` | Yes | What the command does |
| `## Input` | Yes | Table: Parameter, Type, Required, Validation |
| `## Preconditions` | Yes | List of prior conditions |
| `## Postconditions` | Yes | State after execution (includes state transitions) |
| `## Rules Validated` | No | BR-* wiki-links |
| `## Events` | No | Events emitted on success |
| `## Possible Errors` | Yes | Table: Code, Condition, Message |

### Query

| Section | Required | Description |
|---------|----------|-------------|
| `## Purpose` | Yes | What data it returns |
| `## Input` | Yes | Parameters (includes Filters, Sorting, Pagination as sub-sections) |
| `## Output` | Yes | Response structure (table, not TypeScript interface) |
| `## Authorization` | Yes | Access control rules |
| `## Possible Errors` | Yes | Error table |
| `## Examples` | No | Example response JSON |

### Use Case

| Section | Required | Description |
|---------|----------|-------------|
| `## Description` | Yes | Use case summary |
| `## Actors` | Yes | Who participates |
| `## Preconditions` | Yes | Required initial state (includes triggers) |
| `## Main Flow (Happy Path)` | Yes | Step sequence |
| `## Extensions / Alternative Flows` | No | Valid variations |
| `## Postconditions` | Yes | Final state (On Success + On Failure sub-sections) |
| `## Business Rules` | No | BR-* wiki-links |

### Business Rule (BR/BP)

| Section | Required | Description |
|---------|----------|-------------|
| `## Statement` | Yes | Clear description with wiki-links |
| `## Rationale` | Yes | Business justification |
| `## When Applies` | Yes | Activation conditions |
| `## Violation Behavior` | Yes | Consequences |
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

### UI View

| Section | Required | Description |
|---------|----------|-------------|
| `## Purpose` | Yes | Purpose of the view |
| `## Navigation` | Yes | Route, arrives from, navigates to |
| `## Layout` | Yes | ASCII wireframe (includes responsive as sub-section) |
| `## Components` | Yes | Component table by zone |
| `## Data` | Yes | Data sources needed |
| `## View States` | Yes | Loading, empty, error (described in text) |
| `## Behavior` | Yes | On load, main actions, validations |

### Requirement (REQ)

| Section | Required | Description |
|---------|----------|-------------|
| `## Requirements Summary` | No | Overview table |
| `## REQ-NNN.N: Name` | Yes | EARS formalization + Gherkin acceptance criteria |
| `## Traceability Matrix` | No | UC → BR → Test Case links |

### NFR

| Section | Required | Description |
|---------|----------|-------------|
| `## Goal` | Yes | Quality objective and impact |
| `## Metrics` | Yes | Simple table: Metric, Target |
| `## Affected Use Cases` | No | UC wiki-links |
| `## Trade-offs` | No | Accepted trade-offs |

### ADR

| Section | Required | Description |
|---------|----------|-------------|
| `## Context` | Yes | Problem and forces |
| `## Decision` | Yes | What was chosen and why |
| `## Options Considered` | No | Alternatives with pros/cons |
| `## Consequences` | Yes | Positive, negative, neutral |

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
| `rule.template.md` | Business rules (BR) and policies (BP) |
| `command.template.md` | Commands (CQRS write) |
| `query.template.md` | Queries (CQRS read) |
| `process.template.md` | Business processes |
| `use-case.template.md` | Use cases |
| `cross-policy.template.md` | Cross-cutting policies (XP) |
| `ui-view.template.md` | UI views/pages |
| `ui-flow.template.md` | UI navigation flows |
| `ui-component.template.md` | Reusable UI components |
| `prd.template.md` | Product Requirements Document |
| `adr.template.md` | Architecture Decision Records |
| `requirement.template.md` | Functional requirements (EARS) |
| `nfr.template.md` | Non-functional requirements |
| `value-unit.template.md` | Value Units (end-to-end deliverables) |
| `release.template.md` | Release plans |
| `implementation-charter.template.md` | Tech stack and implementation guidelines |
| `_manifest.template.yaml` | Domain manifest (multi-domain) |

---

## Technology-Agnostic Principle

Layers 01-04 are **technology-agnostic**. No TypeScript, no framework references, no pseudocode. Implementation details (code patterns, schemas, stack conventions) belong exclusively in `05-architecture/charter.md`, section "KDD Artifact → Code Mapping".

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
