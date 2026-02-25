# KDD Specification Validation

> Guide to the validation model for KDD specifications. Validation tooling is provided by the companion [kdd-tools](https://github.com/knowledge-driven-dev/kdd-tools) package.

## Summary

KDD supports a three-level validation model that ensures the quality and consistency of specifications in `/specs`. It combines automatic validation (linter) with intelligent analysis (Claude Code skills).

```
┌─────────────────────────────────────────────────────────────────┐
│                    VALIDATION FLOW                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   kdd validate             →    /analyze-entities    →    /fix-spec
│   ─────────────────────         ─────────────────         ─────────
│   Automatic validation          AI Analysis              Automatic
│   (regex, schemas)              (semantic)               correction
│                                                                 │
│   Free, fast                    Uses subscription        Uses subscription
│   Pre-commit / CI               Claude Code              Claude Code
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

> **Note**: The validator and slash commands are provided by [kdd-tools](https://github.com/knowledge-driven-dev/kdd-tools). This document describes the validation model that the kdd-specs templates enable.

---

## 1. Validation Levels

### Level 1: Frontmatter

Validates that YAML metadata complies with the schema defined in the templates.

```yaml
---
id: UC-001                    # Required, specific pattern
kind: use-case                # Required, literal per type
status: draft                 # Required, enum: draft|review|approved|deprecated|superseded
---
```

**Typical errors:**
- Missing required field
- Incorrect ID format
- Disallowed enum value

### Level 2: Structure

Verifies that the document has the required sections according to its type.

**Example for Use Case:**
- `## Description` (required)
- `## Actors` (required)
- `## Preconditions` (required)
- `## Main Flow (Happy Path)` (required)
- `## Extensions / Alternative Flows` (optional)
- `## Postconditions` (required)

**Typical errors:**
- Missing required section
- Multiple H1s
- Empty section

### Level 3: Semantic

Analyzes cross-references and wiki links.

**Validations:**
- Wiki-links `[[entity]]` point to existing entities
- Mentioned events (`EVT-*`) are documented
- Referenced rules (`BR-XXX-NNN`) exist
- Individual requirements (`REQ-NNN.M`) are defined

**Sub-entity detection:**
The index automatically detects individual requirements within their parent files (e.g.: `REQ-001.1`, `REQ-001.2`). Business rules (`BR-XXX-NNN`) are detected from their own files in `specs/01-domain/rules/`.

---

## 2. Intelligent Analysis with Claude Code

For deeper analysis requiring semantic understanding, [kdd-tools](https://github.com/knowledge-driven-dev/kdd-tools) provides slash commands that leverage the Claude Code subscription.

### `/analyze-entities <file>`

Deep AI analysis of a file to detect:

- **Explicit mentions**: Known entities without links
- **Synonyms and variations**: Plurals, equivalent terms
- **Implicit references**: "the customer" → `[[Customer]]`
- **Abbreviations**: UC-001 → `[[UC-001-PlaceOrder]]`
- **Missing entities**: Concepts that should be documented

### `/list-entities`

Generates a complete index of all system entities.

### `/fix-spec <file>`

Automatically fixes detected problems.

**What it fixes:**
- Broken links with similar names
- Unlinked mentions (high confidence)
- Incomplete frontmatter

**What it does NOT fix:**
- Entities that don't exist
- Missing sections
- Problems that require human decision

---

## 3. KDD Templates

Validations are based on templates located in [`/templates/`](/templates/).

### Template Structure

```markdown
---
# @file-pattern: ^UC-\d{3}-.+\.md$
# @path-pattern: 02-behavior/use-cases/

id: UC-NNN                    # @required @pattern: ^UC-\d{3}$
kind: use-case                # @required
status: draft                 # @required @enum: draft|review|approved|deprecated|superseded
---

# UC-NNN: Use Case Title <!-- required pattern: ^UC-\d{3}:\s+.+ -->

## Description <!-- required -->

## Preconditions <!-- required -->

## Main Flow (Happy Path) <!-- required -->

## Postconditions <!-- required -->
```

### Annotations

Only 3 frontmatter annotations are used:

- `@required` — Mandatory field
- `@enum: val1|val2|val3` — Allowed values
- `@pattern: ^REGEX$` — ID format validation

For sections:

- `<!-- required -->` — Mandatory section
- `<!-- optional -->` — Optional section
- `<!-- expects: mermaid|json|gherkin -->` — Expected content type

### Available Templates

| Type | File | Description |
|------|------|-------------|
| entity | `entity.template.md` | Domain entities, roles, external systems |
| event | `event.template.md` | Domain events |
| rule | `rule.template.md` | Business rules and policies |
| use-case | `use-case.template.md` | Use cases (Cockburn-lite) |
| command | `command.template.md` | CQRS commands (state-changing) |
| query | `query.template.md` | CQRS queries (read-only) |
| process | `process.template.md` | Processes (BPMN-lite) |
| cross-policy | `cross-policy.template.md` | Cross-cutting policies |
| requirement | `requirement.template.md` | EARS requirements |
| prd | `prd.template.md` | Product Requirements Document |
| nfr | `nfr.template.md` | Non-functional requirements |
| adr | `adr.template.md` | Architecture Decision Records |
| value-unit | `value-unit.template.md` | Value Units (end-to-end deliverables) |
| release | `release.template.md` | Release plans |
| implementation-charter | `implementation-charter.template.md` | Tech stack and implementation guidelines |
| ui-view | `ui-view.template.md` | Pages/screens |
| ui-flow | `ui-flow.template.md` | Navigation flows |
| ui-component | `ui-component.template.md` | Reusable UI components |

See [`templates/_schema.md`](/templates/_schema.md) for the full template guide.

---

## 4. CI/CD Integration

### Pre-commit Hook

```bash
#!/bin/sh
# .git/hooks/pre-commit

# Validate only modified files in /specs
CHANGED_SPECS=$(git diff --cached --name-only | grep "^specs/.*\.md$")

if [ -n "$CHANGED_SPECS" ]; then
  echo "Validating modified specifications..."
  npx kdd validate $CHANGED_SPECS

  if [ $? -ne 0 ]; then
    echo "Validation failed. Fix the errors before committing."
    exit 1
  fi
fi
```

### GitHub Actions

```yaml
# .github/workflows/validate-specs.yml
name: Validate Specs

on:
  pull_request:
    paths:
      - 'specs/**/*.md'
      - 'templates/**/*.md'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: oven-sh/setup-bun@v1
        with:
          bun-version: latest

      - run: bun install

      - name: Validate specifications
        run: npx kdd validate -o github
```

---

## 5. Recommended Workflow

### When Creating a New Document

```bash
# 1. Copy template
cp templates/use-case.template.md specs/02-behavior/use-cases/UC-011-New.md

# 2. Edit content
# ... write the use case ...

# 3. Validate
npx kdd validate specs/02-behavior/use-cases/UC-011-New.md -v

# 4. Deep analysis (optional, requires kdd-tools)
/analyze-entities specs/02-behavior/use-cases/UC-011-New.md

# 5. Fix
/fix-spec specs/02-behavior/use-cases/UC-011-New.md
```

### Periodic Validation

```bash
# 1. Full validation
npx kdd validate -v

# 2. Review warnings by type
npx kdd validate --level frontmatter
npx kdd validate --level structure
npx kdd validate --level semantics

# 3. Generate listing for manual review (requires kdd-tools)
/list-entities
```

### Before PR

```bash
# Strict validation (must pass with no errors)
npx kdd validate

# If there are important warnings, analyze (requires kdd-tools)
/analyze-entities specs/file-with-warnings.md
```

---

## 6. Troubleshooting

### "Could not load KDD templates"
- Verify that `/templates/` exists
- Files must end in `.template.md`
- Frontmatter must have `# @file-pattern:` or `# @path-pattern:`

### "Entity not found" but it exists
- Regenerate the index
- Verify the file is in a scanned directory (`01-domain/`, `02-behavior/`)
- The name must match (case-insensitive)
- See [Entity Index](./entity-index.md) for more details

### Wiki-link not detected
- Correct format: `[[Name]]` or `[[Name|alias]]`
- No spaces after `[[` or before `]]`
- The target must exist as an entity

---

## References

- [Entity Index](./entity-index.md) — Entity indexing and caching system
- [Writing Conventions](./writing-conventions.md) — Style guide for specifications
- [Template Guide](/templates/_schema.md) — Template format and annotations
- [kdd-tools](https://github.com/knowledge-driven-dev/kdd-tools) — Validator and Claude Code skills
