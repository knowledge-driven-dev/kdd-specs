# KDD Template Guide

This directory contains the canonical templates for each type of specification document.

## Template structure

Each template uses a special format that allows the validator to extract:
1. **Frontmatter schema** - From the YAML block with type annotations
2. **Required sections** - From headings marked with `<!-- required -->`
3. **Expected content** - From blocks marked with `<!-- expects: type -->`

## Annotation conventions

### Frontmatter

```yaml
---
# @type: use-case
# @description: Template for use cases
id: UC-NNN           # @required @pattern: ^UC-\d{3}$
version: 1           # @type: number @default: 1
status: draft        # @enum: draft|proposed|approved|deprecated
actor: Actor         # @required
domain: store        # @optional
tags:                # @type: array
  - use-case
---
```

### Sections

```markdown
## Description <!-- required -->

## Main Flow <!-- required alias: "Happy Path|Main Flow" -->

## Extensions <!-- optional -->
```

### Expected content

```markdown
## Lifecycle

<!-- expects: mermaid:stateDiagram-v2 -->

## Example

<!-- expects: json -->
```

## Template files

| File | Type | Description |
|------|------|-------------|
| `use-case.template.md` | use-case | Use cases (Cockburn-lite) |
| `requirement.template.md` | requirement | EARS requirements |
| `entity.template.md` | entity | Domain entities (see variants below) |
| `event.template.md` | event | Domain events |

### entity.template.md variants

The entity template supports multiple `kind` values:

| Kind | Use | Example | Naming convention |
|------|-----|---------|-------------------|
| `entity` | Domain entity with lifecycle | Order, Cart, Product | PascalCase: `Order.md` |
| `role` | Role/actor that interacts with the system | Customer, Admin | PascalCase: `Customer.md` |
| `system` | External system / integration | STRIPE, WAREHOUSE | UPPERCASE: `STRIPE.md` |
| `rule.template.md` | rule | Business rules |
| `process.template.md` | process | Processes (BPMN-lite) |
| `command.template.md` | command | CQRS commands (state-changing) |
| `query.template.md` | query | CQRS queries (read-only) |
| `prd.template.md` | prd | Product Requirements Document |
| `story.template.md` | story | User stories |
| `nfr.template.md` | nfr | Non-functional requirements |
| `adr.template.md` | adr | Architecture Decision Records |
| `idea.template.md` | idea | Ideas and proposals |
| `value-unit.template.md` | value-unit | Value Units (end-to-end deliverables) |
| `release.template.md` | release | Release plans (group Value Units) |
