# kdd-specs

**kdd-specs** defines the canonical structure, templates, and conventions for organizing KDD (Knowledge-Driven Development) specifications. It is the single source of truth for the KDD methodology — all other KDD tools reference this.

## What is KDD?

**Knowledge-Driven Development** treats specifications as the source of truth. Code is derived and regenerable. Agents and humans share the same structured knowledge base.

### Core Principles

1. **SSoT (Single Source of Truth)**: Specification > Code. Code is a regenerable cache.
2. **Executable Specification**: Examples and contracts become tests and automated checks.
3. **Granularity & Links**: Atomic files with front-matter and wiki-links forming a knowledge graph.
4. **RAG-first**: Agents consult indexed documentation, not the code repo.
5. **Lightweight Governance**: Automated states, coverage, and reviews.

---

## The 5 Layers

KDD organizes specifications into 5 layers, each with a clear purpose:

```
/specs
├── 00-requirements/   # WHY — Business context, objectives, ADRs, value units, releases
├── 01-domain/         # WHAT EXISTS — Entities, events, business rules
├── 02-behavior/       # HOW IT WORKS — Commands, queries, use-cases, processes, policies
├── 03-experience/     # HOW USERS SEE IT — UI views, flows, components
└── 04-verification/   # HOW WE TEST IT — Requirements, BDD scenarios, contracts
```

See [`structure/layers.md`](structure/layers.md) for full dependency rules and conventions.

---

## Artifact Types

| Type            | ID Pattern              | Location                                |
| --------------- | ----------------------- | --------------------------------------- |
| Objective       | `OBJ-NNN`               | `00-requirements/objectives/`           |
| Value Unit      | `UV-NNN`                | `00-requirements/value-units/`          |
| Release         | `REL-NNN`               | `00-requirements/releases/`             |
| ADR             | `ADR-NNNN`              | `00-requirements/decisions/`            |
| Entity          | *(PascalCase)*          | `01-domain/entities/`                   |
| Event           | `EVT-{Entity}-{Action}` | `01-domain/events/`                     |
| Business Rule   | `BR-{ENTITY}-NNN`       | `01-domain/rules/`                      |
| Business Policy | `BP-{TOPIC}-NNN`        | `02-behavior/policies/`                 |
| Command         | `CMD-NNN`               | `02-behavior/commands/`                 |
| Query           | `QRY-NNN`               | `02-behavior/queries/`                  |
| Process         | `PROC-NNN`              | `02-behavior/processes/`                |
| Use Case        | `UC-NNN`                | `02-behavior/use-cases/`                |
| Cross-Policy    | `XP-{TOPIC}-NNN`        | `02-behavior/policies/`                 |
| UI View         | `UI-{Name}`             | `03-experience/views/`                  |
| Requirement     | `REQ-NNN`               | `04-verification/criteria/`             |

---

## Templates

All templates are in [`templates/`](templates/):

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
| `requirement.template.md` | Functional requirements (REQ) |
| `nfr.template.md` | Non-functional requirements |

---

## Adoption

### Option A: Copy templates manually

```bash
cp -r kdd-specs/templates ./kdd/templates
```

### Option B: Use with kdd-tools

```bash
# Activate in Claude Code
claude --plugin-dir /path/to/kdd-tools/platforms/claude-code

# Or in .claude/settings.local.json
{
  "pluginDirs": ["../kdd-tools/platforms/claude-code"]
}
```

### Option C: Semantic search with kdd-engine (optional)

```bash
# Index specs for semantic retrieval
kdd-engine index ./specs

# Start MCP server for Claude Code
kdd-engine serve --port 3001
```

---

## Agent Reference

`kdd.md` is the **compact reference** that all agents read to understand the KDD model. It covers:
- Folder structure and layer dependencies
- Artifact types and ID patterns
- Front-matter schemas by type
- Required sections by artifact type
- Wiki-link syntax and naming conventions

---

## Companion Tools

- **[kdd-tools](https://github.com/knowledge-driven-dev/kdd-tools)** — Skills, commands, agents, and validator for working with kdd-specs. Supports Claude Code (with more platforms coming).
- **[kdd-engine](https://github.com/knowledge-driven-dev/kdd-engine)** — Semantic indexing and retrieval of specs for agent context.

---

## License

MIT
