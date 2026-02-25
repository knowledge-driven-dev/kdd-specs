# Specs-to-Storybook: Process Guide

> How to materialize UI specifications into visual wireframes before coding.

## Executive Summary

This document describes the workflow for transforming UI specifications written in natural language (Markdown) into visual Storybook wireframes, allowing you to validate designs **before writing production code**.

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│   Spec in MD    │  →   │  /generate-story │  →   │   Storybook     │
│  (natural       │      │     (Claude)     │      │   (wireframe)   │
│   language)     │      │                  │      │                 │
└─────────────────┘      └─────────────────┘      └─────────────────┘
```

## Why This Process?

### The Problem

In traditional development:
1. Designer creates a mockup in Figma
2. Developer interprets the mockup
3. Behavioral context is lost
4. Costly iterations in code

### The Solution

With Specs-to-Storybook:
1. **Analyst/Designer** writes a complete spec (structure + behavior)
2. **Claude** generates a visual wireframe in Storybook
3. **Stakeholders** validate in Storybook before coding
4. **Developer** implements with a clear spec and reference wireframe

## UI Specification Types

| Type | Tag | Usage | Template |
|------|-----|-------|----------|
| **Component** | `ui/component` | Reusable elements (button, card, input) | `kdd/templates/ui-component.md` |
| **View** | `ui/view` | Full screens | `kdd/templates/ui-view.md` |
| **Flow** | `ui/flow` | Interaction sequences | `kdd/templates/ui-flow.md` |

## Directory Structure

```
specs/04-interaction/
├── components/           # Atomic components
│   └── ProductCard.md
├── views/                # Views/Pages
│   └── ConfigureOrder.md
└── flows/                # User flows
│   └── CreateAndConfigureOrder.md
└── use-cases/
    └── UC-001-PlaceOrder.md

kdd/templates/
├── ui-component.template.md       # Component template
├── ui-view.template.md            # View template
└── ui-flow.template.md            # Flow template

apps/web/components/features/
└── {feature}/
    ├── {component}.stories.tsx  # Generated stories
    └── {component}.tsx          # Implementation (later)
```

## Step-by-Step Workflow

### Step 1: Create the Specification

Use the corresponding template from `kdd/templates/`:

```bash
# Copy template
cp kdd/templates/ui-view.md specs/04-experience/views/MyNewView.md
```

Complete the sections:
- **Front-matter**: tags, links to entities/use cases
- **Description**: View purpose
- **Layout**: Structure in ASCII art
- **States**: Loading, empty, error, success
- **Behavior**: Interactions and validations
- **Accessibility**: a11y considerations

### Step 2: Generate the Story

Run the Claude slash command:

```
/generate-story specs/04-experience/views/MyNewView.md
```

Claude:
1. Reads and understands the spec
2. Translates ASCII art to shadcn/ui components
3. Generates mock data based on referenced entities
4. Creates stories for each described state
5. Saves to `apps/web/components/features/{feature}/`

### Step 3: Visualize in Storybook

```bash
bun run storybook
```

Navigate to the generated story and validate:
- Does the structure match the spec?
- Are the states correct?
- Does the responsiveness work?

### Step 4: Iterate

If something is not right:
1. **Adjust the spec** (not the story directly)
2. **Regenerate** with `/generate-story`
3. The story reflects the changes

### Step 5: Approve and Implement

Once validated:
1. Mark the spec as `status: approved`
2. Implement the real component based on:
   - The spec (behavior)
   - The wireframe (visual structure)
3. Replace the wireframe with the real implementation

## Anatomy of a Spec

### Front-matter (required)

```yaml
---
tags:
  - ui/view                    # Spec type
status: draft                  # draft | review | approved | implemented
version: "1.0"
links:
  entities:                    # Domain entities used
    - "[[Order]]"
    - "[[Product]]"
  use-cases:                   # Use cases implemented
    - "[[UC-002-Configure-Products]]"
  components:                  # Referenced components
    - "[[ProductCard]]"
storybook:
  category: "Views"            # Category in Storybook
  auto-generate: true          # Whether Claude should generate it
---
```

### ASCII Wireframe (recommended)

The ASCII art is interpreted by Claude:

```ascii
┌──────────────────────────────────────┐
│  [← Back]    Title    [Badge]        │  ← Header
├──────────────────────────────────────┤
│                                      │
│  ┌────────────┐  ┌────────────┐      │  ← Card grid
│  │  Card 1    │  │  Card 2    │      │
│  └────────────┘  └────────────┘      │
│                                      │
│  [Primary Button]  [Secondary Btn.]  │  ← Actions
└──────────────────────────────────────┘
```

Claude translates:
- `┌───┐` → `<Card>` or `<div className="border rounded">`
- `[Text]` → `<Button>`
- `←` → Lucide icon
- Proportions → Tailwind classes

### States (critical for UX)

Document all view states:

```markdown
## View States

### Loading
- Show skeleton
- Disable interactions

### Empty
- "No data" message
- CTA to create/add

### Error
- Error message
- Retry button

### Success (default)
- Data loaded
- Interactions enabled
```

## Useful Commands

| Command | Description |
|---------|-------------|
| `/generate-story specs/04-experience/...` | Generate story from spec |
| `bun run storybook` | Start local Storybook |
| `bun run build:storybook` | Static Storybook build |

## Real Examples

### Spec: ProductCard

**File**: `specs/04-experience/components/ProductCard.md`

**Key sections**:
- Props documented with types
- ASCII art for each size (sm, md, lg)
- States: default, hover, selected, loading
- Deletion behavior with confirmation

**Generated story**: `apps/web/components/features/order/product-card.stories.tsx`

**Included stories**:
- Default, Selected, ReadOnly, Loading
- SizeSmall, SizeMedium, SizeLarge
- GridOfProducts, InteractiveDemo

### Spec: ConfigureOrder (View)

**File**: `specs/04-experience/views/ConfigureOrder.md`

**Key sections**:
- Complete layout with header, main, footer
- Responsive: desktop, tablet, mobile
- States: loading, empty, partial, configured, error
- Behavior for each interaction

**Generated story**: `apps/web/components/features/order/configure-order-view.stories.tsx`

**Included stories**:
- Loading, Empty, Partial, Configured, Error
- MobileView, TabletView
- InteractiveDemo (functional)

## Best Practices

### DO

- **Write complete specs** before generating
- **Include all UI states**
- **Reference entities** for realistic mock data
- **Use ASCII art** for complex layouts
- **Document behavior**, not just appearance
- **Iterate on the spec**, not the story

### DON'T

- Do not edit generated stories directly (they will be lost on regeneration)
- Do not omit error/loading states
- Do not write specs without front-matter
- Do not generate without validating that the spec is complete

## Troubleshooting

### The story does not generate correctly

1. Verify that the spec has valid front-matter
2. Make sure the ASCII art uses standard Unicode characters
3. Check that the referenced entities exist

### Storybook does not find the story

1. Verify the file location
2. Check that `.storybook/main.ts` includes the correct pattern
3. Restart Storybook

### The wireframe does not resemble the spec

1. Be more explicit in the ASCII art
2. Add descriptions to each section
3. Specify shadcn/ui components to use

## References

- **Original plan**: `wip/plan-specs-to-storybook.md`
- **Templates**: `kdd/templates/ui-*.md`
- **Slash command**: `.claude/commands/generate-story.md`
- **Examples**:
  - `specs/04-experience/components/ProductCard.md`
  - `specs/04-experience/views/ConfigureOrder.md`
