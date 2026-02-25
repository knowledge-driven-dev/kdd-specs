---
kind: ui-component
status: draft
links:
  entities: []
  use-cases: []
  commands: []
---

# {{ComponentName}}

## Purpose

<!-- 1-3 lines: what problem does this component solve for the user -->

## Data

<!-- What information it displays. Simple list, no TS types -->

- field1 — brief description
- field2 — brief description
- optionalField? — when it appears

## Structure

<!-- ONE wireframe showing the anatomy. States come later -->

```
┌─────────────────────────────────────┐
│ [Status]                        [⭐] │
│ ─────────────────────────────────── │
│ Item title                          │
│                                      │
│ 📊 metric1  │  📊 metric2          │
│                                      │
│ [Main Action]     [Edit]       [⋮]  │
└─────────────────────────────────────┘
```

## Actions

<!-- What the user can do. Format: Action → Consequence -->

| Action | Result | Navigation |
|--------|--------|------------|
| Click on card | Opens detail | → [[VIEW-Detail]] |
| Click on main action | Starts process X | → [[VIEW-Process]] |
| Click on menu (⋮) | Shows options: Edit, Duplicate, Delete | — |
| Delete | Requires confirmation, executes [[CMD-Delete]] | — |

## Functional States

<!-- Only states that change WHAT is shown, not how it looks -->

### By entity state

<!-- If the component changes based on the state of the data it displays -->

| State | Main action | Available actions |
|-------|-------------|-------------------|
| draft | "Configure" | edit, delete |
| active | "Continue" | menu only |
| completed | "View result" | duplicate |

### Loading

```
┌─────────────────────────────────────┐
│ ░░░░░░░░                            │
│ ░░░░░░░░░░░░░░░░░░░░                │
│ ░░░░░  │  ░░░░░                     │
└─────────────────────────────────────┘
```

### Error / Empty

<!-- Only if applicable and significantly changes the layout -->

## Variants

<!-- Only if there are functional variants, not style variants -->

- **Compact** (mobile): Hides secondary actions, menu only
- **Expanded**: Shows additional metrics

## Connections

- **Entities**: [[Entity1]], [[Entity2]]
- **Use cases**: [[UC-001]], [[UC-002]]
- **Commands**: [[CMD-Create]], [[CMD-Delete]]
