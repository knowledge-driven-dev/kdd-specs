# KDD - Knowledge-Driven Development

**KDD (Knowledge-Driven Development)** is a methodology where structured specifications are the source of truth and code is derived/regenerable.

## Core Principles

1. **SSoT (Single Source of Truth)**: Specification > Code. Code is a regenerable cache.
2. **Executable Specification**: Examples and contracts become tests and checks.
3. **Granularity & Links**: Atomic files with front-matter and wiki-links forming a knowledge graph.
4. **RAG-first**: Agents consult indexed documentation, not the code repo.
5. **Lightweight Governance**: Automated states, coverage, and reviews.

## The 6 Layers

KDD organizes specifications into layered directories, each with a clear purpose and dependency rules:

```
/specs
├── 00-inbox/              # Unprocessed ideas
├── 01-problem/            # WHY - Business context (PRD, ADR)
├── 02-domain/             # WHAT EXISTS - Domain model (entities, events, rules)
├── 03-capabilities/       # WHAT IT CAN DO - Operations (commands, queries, processes)
├── 04-interaction/        # HOW USERS USE IT - (use cases, views, flows)
└── 05-verification/       # HOW WE TEST IT - (criteria, examples, contracts)
```

### Layer Dependencies

```
05-verification  →  can reference all layers
04-interaction   →  can reference 03, 02, 01
03-capabilities  →  can reference 02, 01
02-domain        →  can only reference 01
01-problem       →  references nothing
```

**Rule**: Higher layers CAN reference lower layers. Lower layers CANNOT reference higher layers.

## Quick Start

1. Copy the example structure into your project:
   ```bash
   cp -r examples/specs/ your-project/specs/
   ```

2. Start documenting using the templates in `templates/`.

3. Read `kdd.md` for the full methodology reference.

## Templates

| Template | For | Location |
|----------|-----|----------|
| `entity.template.md` | Domain entities | `02-domain/entities/` |
| `event.template.md` | Domain events | `02-domain/events/` |
| `rule.template.md` | Business rules | `02-domain/rules/` |
| `command.template.md` | Commands (CQRS write) | `03-capabilities/commands/` |
| `query.template.md` | Queries (CQRS read) | `03-capabilities/queries/` |
| `process.template.md` | Business processes | `03-capabilities/processes/` |
| `use-case.template.md` | Use cases | `04-interaction/use-cases/` |
| `ui-view.template.md` | UI views/pages | `04-interaction/views/` |
| `ui-flow.template.md` | UI navigation flows | `04-interaction/flows/` |
| `ui-component.template.md` | Reusable UI components | `04-interaction/components/` |
| `prd.template.md` | Product Requirements | `01-problem/` |
| `adr.template.md` | Architecture Decisions | `01-problem/decisions/` |
| `requirement.template.md` | Functional requirements | `05-verification/criteria/` |
| `story.template.md` | User stories | - |
| `nfr.template.md` | Non-functional requirements | - |
| `idea.template.md` | Raw ideas | `00-inbox/` |
| `implementation-charter.template.md` | Implementation charters | - |

## Documentation

| Document | Description |
|----------|-------------|
| [kdd.md](kdd.md) | Full methodology reference |
| [Introduction](docs/introduction.md) | Introduction to KDD |
| [Writing Conventions](docs/writing-conventions.md) | Naming and formatting rules |
| [Feature Discovery](docs/feature-discovery.md) | How to refine ideas into specs |
| [Entity Index](docs/entity-index.md) | How entity indexing works |
| [Validation](docs/validation.md) | Specification validation |
| [Knowledge Graph](docs/graph.md) | The specification graph |
| [Storybook Workflow](docs/storybook-workflow.md) | Specs to Storybook |
| [Specs to Storybook](docs/specs-to-storybook.md) | Detailed spec-to-story mapping |

### Layer Documentation

| Layer | Description |
|-------|-------------|
| [01 - Problem](docs/layers/01-problem.md) | Business context |
| [02 - Domain](docs/layers/02-domain.md) | Domain model |
| [03 - Capabilities](docs/layers/03-capabilities.md) | System operations |
| [04 - Interaction](docs/layers/04-interaction.md) | User interaction |
| [05 - Verification](docs/layers/05-verification.md) | Testing & validation |
| [06 - Architecture](docs/layers/06-architecture.md) | Technical architecture |

## Companion Tools

- **[kdd-claude-code](https://github.com/knowledge-driven-dev/kdd-claude-code)** - Claude Code plugin with commands for KDD workflows
- **[kdd-spec-validator](https://github.com/knowledge-driven-dev/kdd-spec-validator)** - CLI tool for validating KDD specifications

## License

MIT
