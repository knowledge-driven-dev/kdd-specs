# @file-pattern: ^implementation-charter\.md$
---
id: ARCH-CHARTER-XXXX          # @required @pattern: ^ARCH-CHARTER-[A-Z0-9\-]+$
kind: implementation-charter   # @required
status: draft                  # @required @enum: [draft, review, approved, deprecated]
stack_version: YYYY-MM         # @required
supersedes: []                 # @optional
adr_refs:                      # @optional @description: List of related technology ADRs
  - ADR-XXXX
---

# Implementation Charter · {Scope}

> Describe the scope (API, Web, Data, etc.) and purpose of the charter.

## 1. Official Stack

| Layer | Technology | Minimum Version | ADR |
|-------|------------|-----------------|-----|
| Runtime | ... | ... | [[ADR-XXXX]] |
| Backend | ... | ... | [[ADR-XXXX]] |

## 2. Repository Topology

```
{Relevant structure}
```

Key dependency rules (link to ADRs or documentation).

## 3. KDD Artifact → Code Mapping

| KDD Artifact | Minimum Output | Destination Path |
|--------------|----------------|------------------|
| `CMD-*` | ... | ... |

## 4. Code Conventions

- Style/naming summary.
- Links to examples in the repository (e.g., `CLAUDE.md`).

## 5. Tooling & Pipelines

| Action | Command |
|--------|---------|
| ... | ... |

## 6. Governance

- Owners and update process.
- How to version the charter and record supersedes.

## 7. Agent Compatibility

Indicate how agents should read this charter (metadata, snippets, templates).

## 8. Next Steps

List of tasks or follow-up links.
