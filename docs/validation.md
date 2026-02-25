# KDD Specification Validation

> Complete guide to the validation system for KDD (Knowledge-Driven Development) documentation.

## Summary

The project features a three-level validation system that ensures the quality and consistency of specifications in `/specs`. It combines automatic validation (script) with intelligent analysis (Claude Code).

```
┌─────────────────────────────────────────────────────────────────┐
│                    VALIDATION FLOW                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   bun run validate:specs     →    /analyze-entities    →    /fix-spec
│   ─────────────────────           ─────────────────         ─────────
│   Automatic validation            AI Analysis              Automatic
│   (regex, schemas)                (semantic)               correction
│                                                                 │
│   Free, fast                      Uses subscription        Uses subscription
│   Pre-commit / CI                 Claude Code              Claude Code
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 1. Automatic Validator (spec-validator)

### Location
```
scripts/spec-validator/
├── index.ts                 # Main CLI
├── lib/
│   ├── parser.ts            # Markdown + frontmatter parser
│   ├── entity-index.ts      # Known entity index
│   ├── template-loader.ts   # Loads schemas from templates
│   └── reporter.ts          # Result formatting
├── validators/
│   ├── frontmatter.ts       # Level 1: Metadata validation
│   ├── structure.ts         # Level 2: Document structure
│   └── semantics.ts         # Level 3: References and links
└── schemas/
    ├── frontmatter.ts       # Zod schemas (legacy)
    └── structure.ts         # Section templates (legacy)
```

### Validation Levels

#### Level 1: Frontmatter
Validates that YAML metadata complies with the schema defined in the templates.

```yaml
---
id: UC-001                    # Required, specific pattern
kind: use-case                # Literal per type
status: draft                 # Enum: draft|proposed|approved|deprecated
actor: Registered Customer    # Required for use-cases
---
```

**Typical errors:**
- Missing required field
- Incorrect ID format
- Disallowed enum value

#### Level 2: Structure
Verifies that the document has the required sections according to its type.

**Example for Use Case:**
- `## Description` (required)
- `## Preconditions` (required)
- `## Main Flow` (required)
- `## Alternative Flows` (optional)
- `## Postconditions` (required)

**Typical errors:**
- Missing required section
- Multiple H1s
- Empty section

#### Level 3: Semantic
Analyzes cross-references and wiki links.

**Validations:**
- Wiki-links `[[entity]]` point to existing entities
- Mentioned events (`EVT-*`) are documented
- Referenced rules (`BR-XXX-NNN`) exist
- Individual requirements (`REQ-NNN.M`) are defined

**Sub-entity detection:**
The index automatically detects individual requirements within their parent files (e.g.: `REQ-001.1`, `REQ-001.2`). Business rules (`BR-XXX-NNN`) are detected from their own files in `specs/02-domain/rules/`.

### Commands

```bash
# Validate all of /specs
bun run validate:specs

# Validate with details (shows info and suggestions)
bun run validate:specs -v

# Validate only one level
bun run validate:specs --level frontmatter
bun run validate:specs --level structure
bun run validate:specs --level semantics

# Validate specific directory
bun run validate:specs specs/02-domain

# Auto-fix links (semantic level)
bun run validate:specs --fix

# GitHub Actions format
bun run validate:specs -o github

# JSON format (for integration)
bun run validate:specs -o json
```

### Example Output

```
🔍 Spec Validator - KDD Documentation Linter

  Target: specs
  Level:  all
  Fix:    disabled

  Found 56 spec files

📊 Entity Index:
   Total: 159 indexed entities
   Entities: 11
   Events: 9
   Rules: 53
   Use Cases: 10
   Requirements: 75
   └─ Individual requirements: 65

specs/02-domain/rules/BR-ORDERITEM-003.md
  ✗ Missing required field "id" in frontmatter:1
  ⚠ Section "Implementation" appears to be empty:45

behavior/use-cases/UC-001-PlaceOrder.md
  ⚠ Link [[EVT-Order-Placed]] does not match any entity:83
  ℹ "Customer" should be a link to [[Customer]]:15

──────────────────────────────────────────────────
Summary: 56 files, 3 errors, 12 warnings

✗ Validation failed
```

---

## 2. Intelligent Analysis with Claude Code

For deeper analysis requiring semantic understanding, we use slash commands that leverage the Claude Code subscription.

### `/analyze-entities <file>`

Deep AI analysis of a file to detect:

- **Explicit mentions**: Known entities without links
- **Synonyms and variations**: Plurals, accents, equivalent terms
- **Implicit references**: "the customer" → `[[Customer]]`
- **Abbreviations**: UC-001 → `[[UC-001-PlaceOrder]]`
- **Missing entities**: Concepts that should be documented

**Usage:**
```
/analyze-entities specs/vision/charter.md
```

**When to use it:**
- After creating a new document
- When the automatic validator reports many "info" entries
- For complex documents with many references

### `/list-entities`

Generates a complete index of all system entities.

**Usage:**
```
/list-entities
```

**When to use it:**
- To get an overview of the domain
- Before creating new entities (avoid duplicates)
- For documentation or onboarding

### `/fix-spec <file>`

Automatically fixes detected problems.

**Usage:**
```
/fix-spec specs/vision/charter.md
```

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

Validations are based on templates located in `/kdd/templates/`.

### Template Structure

```markdown
---
# @type: use-case
# @description: System use case
# @file-pattern: ^UC-\d{3}.*\.md$

id: UC-NNN                    # @required @pattern: ^UC-\d{3}$
kind: use-case                # @literal: use-case
status: draft                 # @enum: draft|proposed|approved|deprecated
actor: Primary Actor          # @required
---

# UC-NNN: Use Case Name <!-- required pattern: ^UC-\d{3}: -->

## Description <!-- required -->

## Preconditions <!-- required -->

## Main Flow <!-- required -->
<!-- expects: gherkin -->

## Postconditions <!-- required -->
```

### Available Annotations

**For frontmatter:**
- `@required` - Mandatory field
- `@optional` - Optional field (default)
- `@pattern: regex` - Validate format
- `@enum: val1|val2|val3` - Allowed values
- `@literal: value` - Exact expected value
- `@type: string|number|boolean|array|date`
- `@contains: value` - Array must contain value
- `@description: text` - Field description

**For sections:**
- `<!-- required -->` - Mandatory section
- `<!-- optional -->` - Optional section
- `<!-- alias: "Alt1|Alt2" -->` - Alternative names
- `<!-- expects: mermaid|json|gherkin|typescript -->` - Expected content

### Available Templates

| Type | File | Description |
|------|------|-------------|
| use-case | `use-case.template.md` | Use cases |
| requirement | `requirement.template.md` | EARS requirements |
| entity | `entity.template.md` | Domain entities |
| event | `event.template.md` | System events |
| rule | `rule.template.md` | Business rules |
| process | `process.template.md` | Processes/flows |
| story | `story.template.md` | User stories |
| nfr | `nfr.template.md` | Non-functional requirements |
| adr | `adr.template.md` | Architecture Decision Records |
| prd | `prd.template.md` | Product Requirements Document |
| ui-component | `ui-component.template.md` | UI components |
| ui-view | `ui-view.template.md` | UI views/pages |
| ui-flow | `ui-flow.template.md` | UI navigation flows |

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
  bun run validate:specs $CHANGED_SPECS

  if [ $? -ne 0 ]; then
    echo "❌ Validation failed. Fix the errors before committing."
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
      - 'kdd/templates/**/*.md'

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
        run: bun run validate:specs -o github
```

---

## 5. Recommended Workflow

### When Creating a New Document

```bash
# 1. Copy template
cp kdd/templates/use-case.template.md specs/03-behavior/use-cases/UC-011-New.md

# 2. Edit content
# ... write the use case ...

# 3. Validate
bun run validate:specs specs/03-behavior/use-cases/UC-011-New.md -v

# 4. Deep analysis (optional)
/analyze-entities specs/03-behavior/use-cases/UC-011-New.md

# 5. Fix
/fix-spec specs/03-behavior/use-cases/UC-011-New.md
```

### Periodic Validation

```bash
# 1. Full validation
bun run validate:specs -v

# 2. Review warnings by type
bun run validate:specs --level frontmatter
bun run validate:specs --level structure
bun run validate:specs --level semantics

# 3. Regenerate entity cache (if entities were added/modified)
bun run specs:index

# 4. Generate listing for manual review
/list-entities
```

### Before PR

```bash
# Strict validation (must pass with no errors)
bun run validate:specs

# If there are important warnings, analyze
/analyze-entities specs/file-with-warnings.md
```

---

## 6. Troubleshooting

### "Could not load KDD templates"
- Verify that `/kdd/templates/` exists
- Files must end in `.template.md`
- Frontmatter must have `# @type: name`

### "Entity not found" but it exists
- Regenerate the index: `bun run specs:index`
- Verify the file is in a scanned directory (`02-domain/`, `03-behavior/`)
- The name must match (case-insensitive)
- See [Entity Index](./entity-index.md) for more details

### Validator too slow
- Use `--level` to validate only one level
- Specify a concrete directory instead of all of `/specs`
- The entity index is regenerated on each execution; consider using `specs/_index.json` as cache

### Wiki-link not detected
- Correct format: `[[Name]]` or `[[Name|alias]]`
- No spaces after `[[` or before `]]`
- The target must exist as an entity

---

## References

- [Entity Index](./entity-index.md) - Entity indexing and caching system
- [Writing Conventions](./writing-conventions.md) - Style guide for specifications
- [KDD Templates](/kdd/templates/_schema.md) - Template format guide
- [spec-validator README](/scripts/spec-validator/README.md) - Validator technical documentation
- [CLAUDE.md](/CLAUDE.md) - General project instructions
