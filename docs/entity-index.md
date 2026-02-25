# KDD Entity Index

> Automatic indexing system for domain entities, optimized for use by Claude, scripts, and humans.

## General Description

The entity index is a static cache that lists all known entities in the specifications repository. It facilitates:

1. **Quick context for Claude**: Loading `specs/_index.md` provides immediate visibility of all entities
2. **Auto-linking**: The validator uses the index to detect unlinked mentions
3. **Human navigation**: Quick reference to find specification files
4. **Semantic validation**: Verifying that references point to existing entities

---

## Generated Files

| File | Format | Primary Use |
|------|--------|-------------|
| `specs/_index.md` | Markdown | Human reading, Claude context |
| `specs/_index.json` | JSON | Programmatic use in scripts |

### specs/_index.md

Markdown document with tables organized by entity type:

- **Domain Entities**: Aggregates, Entities, Value Objects
- **Events**: System events (EVT-*)
- **Business Rules**: Process rules (`BR-NNN-Name`)
- **Use Cases**: UC-NNN
- **Requirements**: Groups (REQ-NNN-*) and individual (REQ-NNN.M)
- **Processes**: PRC-NNN

Also includes a complete list of recognized **search terms** for auto-linking.

### specs/_index.json

JSON structure for programmatic use:

```json
{
  "generatedAt": "2025-12-11T22:37:27.500Z",
  "totalEntities": 159,
  "byType": {
    "entity": 11,
    "event": 9,
    "rule": 8,
    "individual-rule": 45,
    "use-case": 10,
    "requirement": 10,
    "individual-requirement": 65,
    "process": 1
  },
  "entities": [
    {
      "name": "Order",
      "id": null,
      "aliases": ["Orders", "Purchase", "Transaction"],
      "type": "entity",
      "subtype": null,
      "path": "02-domain/entities/Order.md",
      "line": null,
      "parentId": null,
      "searchTerms": ["order", "orders", "purchase", "transaction"]
    },
    {
      "name": "Sequential dependency of order items",
      "id": "BR-009-ItemSequence",
      "aliases": ["BR-009-ItemSequence"],
      "type": "rule",
      "subtype": "business-rule",
      "path": "02-domain/rules/BR-009-ItemSequence.md",
      "line": 1,
      "parentId": null,
      "searchTerms": ["br-009-itemsequence", "sequential dependency"]
    }
  ]
}
```

---

## Index Regeneration

### Command

```bash
bun run specs:index
```

### When to Regenerate

The index must be regenerated when:

| Situation | Action |
|-----------|--------|
| A new entity is created | Regenerate |
| An entity is renamed | Regenerate |
| Aliases are added in frontmatter | Regenerate |
| Individual rules/requirements are added | Regenerate |
| Content is modified without changing structure | Not necessary |
| Before running full validation | Recommended |

### Suggested Automation

#### Pre-commit hook (optional)

```bash
# .husky/pre-commit
bun run specs:index
git add specs/_index.md specs/_index.json
```

#### In CI/CD

```yaml
# .github/workflows/validate-specs.yml
- name: Regenerate entity index
  run: bun run specs:index

- name: Validate specifications
  run: bun run validate:specs:ci
```

### Command Output

```
🔍 Scanning entities in specs/...
   Found 159 entities
✅ Generated: specs/_index.json
✅ Generated: specs/_index.md

📊 Summary:
   entity: 11
   event: 9
   individual-requirement: 65
   individual-rule: 45
   process: 1
   requirement: 10
   rule: 8
   use-case: 10
```

---

## Usage in Claude and Commands

### Loading Entity Context

For Claude to have quick access to all entities when writing or reviewing specifications:

```markdown
<!-- In a command .claude/commands/*.md -->
First, read the entity index:
- `specs/_index.md` for complete context
```

### Example: /analyze-entities Command

The `/analyze-entities` command uses the index to detect unlinked entity mentions in a specific file.

### Programmatic Usage

```typescript
// In a script
import indexData from '../specs/_index.json'

// Find entity by name
const order = indexData.entities.find(e => e.name === 'Order')

// Filter by type
const rules = indexData.entities.filter(e => e.type === 'rule')

// Search by term
const term = 'cart'
const matches = indexData.entities.filter(e =>
  e.searchTerms.includes(term)
)
```

---

## Indexed Entity Structure

### EntityEntry Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Canonical name (from H1 or filename) |
| `id` | string? | Unique ID (BR-009-ItemSequence, REQ-001.1, etc.) |
| `aliases` | string[] | Alternative names from frontmatter |
| `type` | enum | entity, event, rule, use-case, requirement, process |
| `subtype` | enum? | individual-rule, individual-requirement |
| `path` | string | Relative path from specs/ |
| `line` | number? | Line where it is defined (for sub-entities) |
| `parentId` | string? | Parent entity (for sub-entities) |
| `searchTerms` | string[] | Normalized terms for search |

### Sub-entity Detection

The indexer automatically detects individual rules and requirements within parent files:

```markdown
<!-- In BR-009-ItemSequence.md -->
## BR-009-ItemSequence: Sequential dependency
...
```

```markdown
<!-- In REQ-001-Place-Order.md -->
## REQ-001.1: Order Creation (Event-Driven)
...

## REQ-001.2: Title Length Validation
...
```

---

## Scanned Directories

The indexer searches for entities in these directories:

| Directory | Type |
|-----------|------|
| `specs/02-domain/entities/` | entity |
| `specs/02-domain/events/` | event |
| `specs/02-domain/rules/` | rule |
| `specs/03-behavior/use-cases/` | use-case |
| `specs/03-behavior/requirements/` | requirement |
| `specs/03-behavior/processes/` | process |

It also supports legacy structure without numeric prefixes (`specs/domain/`, `specs/behavior/`).

---

## Validator Integration

The semantic validator (`bun run validate:specs`) uses the index to:

1. **Verify broken links**: `[[Entity]]` must point to a known entity
2. **Detect unlinked mentions**: Text that matches entities but is not linked
3. **Validate cross-references**: EVT-*, UC-*, BR-* must exist
4. **Suggest corrections**: Proposes the correct link based on similarity

---

## Troubleshooting

### The index shows 0 entities

**Cause**: The specs directories have a different structure than expected.

**Solution**: Verify that the directories `specs/02-domain/` and `specs/03-behavior/` exist with the correct subdirectories.

### An entity does not appear in the index

**Possible causes**:
1. The file does not have a `.md` extension
2. The file starts with `_` (ignored by convention)
3. The file is in a non-scanned directory

### Search terms don't match

**Cause**: Normalization removes accents and converts to lowercase.

**Solution**: Add aliases in the file's frontmatter:

```yaml
---
aliases:
  - Alternative term
  - Other name
---
```

---

## References

- [Specification Validation](./validation.md)
- [Writing Conventions](./writing-conventions.md)
- [Script: generate-index.ts](/scripts/spec-validator/generate-index.ts)
