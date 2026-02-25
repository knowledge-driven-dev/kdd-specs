# Storybook in KDD: From Wireframes to Components

## Introduction

This document explains how Storybook integrates into the KDD (Knowledge-Driven Development) methodology as a bridge between specifications and production code.

## What is Storybook?

Storybook is a tool for developing and documenting UI components in isolation. It allows you to visualize components in different states without needing to run the entire application.

## The Role of Storybook in KDD

In KDD, Storybook serves two distinct functions depending on the project phase:

```
┌─────────────────────────────────────────────────────────────────┐
│                    KDD + STORYBOOK FLOW                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  SPECS (markdown)                                               │
│       │                                                         │
│       ▼                                                         │
│  WIREFRAMES (in .stories.tsx)    ← Design/validation phase      │
│       │                                                         │
│       ▼                                                         │
│  REAL COMPONENTS (.tsx)          ← Implementation phase          │
│       │                                                         │
│       ▼                                                         │
│  UPDATED STORIES                 ← Living documentation          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Phase 1: Wireframes as Executable Documentation

### What is a wireframe in Storybook?

A wireframe is a simplified component that lives **inside** the `.stories.tsx` file. Its purpose is:

- Visualize the design before implementing
- Validate the specification with stakeholders
- Document component states and variants
- Serve as a reference for the real implementation

### Wireframe Structure

```tsx
// product-form.stories.tsx

/**
 * Wireframe generated from: specs/04-interaction/views/UI-ProductForm.md
 * Status: draft (pending real implementation)
 */

// The component lives INSIDE the .stories.tsx file
function ProductFormWireframe({ onSave, onCancel, isLoading }) {
  // Basic implementation to visualize the design
  return (
    <div className="...">
      {/* Simplified UI */}
    </div>
  )
}

// Stories that document different states
export const Default: Story = { args: { ... } }
export const Loading: Story = { args: { isLoading: true } }
export const WithError: Story = { args: { error: "..." } }
```

### Wireframe Characteristics

| Aspect | Wireframe |
|--------|-----------|
| **Location** | Inside `.stories.tsx` |
| **Purpose** | Validate design, document |
| **Business logic** | Simulated or absent |
| **API calls** | No |
| **Used in production** | No |
| **Reusable** | No |

## Phase 2: Real Components

### Transition from Wireframe to Real Component

When the real component is implemented, the flow is:

1. **Create the component** in a separate `.tsx` file
2. **Update the story** to import the real component
3. **Remove the wireframe** from the `.stories.tsx` file

### Transition Example

**Before (wireframe):**
```tsx
// product-form.stories.tsx

// Wireframe lives here
function ProductFormWireframe({ ... }) {
  return <div>...</div>
}

const meta: Meta<typeof ProductFormWireframe> = {
  component: ProductFormWireframe,
}
```

**After (real component):**
```tsx
// product-form.tsx (NEW FILE)
export function ProductForm({ ... }) {
  // Complete implementation with hooks, API, etc.
}
```

```tsx
// product-form.stories.tsx (UPDATED)
import { ProductForm } from './product-form'  // Import the real one

const meta: Meta<typeof ProductForm> = {
  component: ProductForm,  // Use the real one
}

// Stories remain, now they document the real component
export const Default: Story = { ... }
```

### Real Component Characteristics

| Aspect | Real Component |
|--------|----------------|
| **Location** | Own `.tsx` file |
| **Purpose** | Production code |
| **Business logic** | Complete |
| **API calls** | Yes (mocked in stories) |
| **Used in production** | Yes |
| **Reusable** | Yes |

## Co-location Convention

### What is Co-location?

Co-location means that related files live together:

```
components/features/order/
├── product-form.tsx           ← Component
├── product-form.stories.tsx   ← Documentation/Stories
├── product-form.test.tsx      ← Tests
└── product-form.types.ts      ← Types (optional)
```

### Why Co-location?

1. **Discoverability**: When opening the component, you immediately see its documentation and tests
2. **Maintenance**: Changes to the component remind you to update stories and tests
3. **Modularity**: Moving or deleting a component moves all its documentation
4. **Industry standard**: It is the convention recommended by Storybook

### Storybook Configuration

The `.storybook/main.ts` file searches for stories across the entire project:

```typescript
const config: StorybookConfig = {
  stories: [
    '../components/**/*.stories.@(js|jsx|ts|tsx)',
  ],
}
```

## Organization in the Storybook Sidebar

### Hierarchy by Categories

Stories are organized using the `title` field in the meta:

```tsx
// Base UI components
const meta = { title: 'UI/Button', ... }

// Specific features
const meta = { title: 'Features/Order/ProductForm', ... }

// Views/Full pages
const meta = { title: 'Views/Dashboard', ... }
```

### Sidebar Result

```
├── UI/
│   ├── Button
│   └── Input
├── Features/
│   └── Order/
│       ├── ProductForm
│       └── ProductCard
└── Views/
    └── Dashboard
```

## Navigation Between Stories

### Connecting Components According to Specs

KDD specifications define interactions that can open other components:

```markdown
<!-- In UI-ProductGenerateModal.md -->

### Click on "Generate"
- **Trigger**: Click on [Generate] button
- **Opens**: [[UI-ProductForm]] → WithGeneratedContent
```

### Implementation with linkTo

```tsx
import { linkTo } from '@storybook/addon-links'

const navigationLinks = {
  onGenerateSuccess: linkTo('Features/Order/ProductForm', 'WithGeneratedContent'),
  onCancel: linkTo('Views/ConfigureOrder', 'Default'),
}

export const WithNavigation: Story = {
  render: () => (
    <ProductGenerateModal
      onGenerate={(prompt) => {
        // Simulate successful generation
        setTimeout(() => {
          navigationLinks.onGenerateSuccess()
        }, 1000)
      }}
      onCancel={() => navigationLinks.onCancel()}
    />
  ),
}
```

## Recommended Workflow

### 1. Create Specification

```markdown
<!-- specs/04-interaction/views/UI-MyComponent.md -->

# MyComponent

## Props
...

## States
...

## Interactions
...
```

### 2. Generate Wireframe in Story

```bash
# Use Claude Code command
/generate-story for UI-MyComponent
```

### 3. Validate with Stakeholders

- Review wireframe in Storybook
- Iterate on design if necessary
- Update spec and regenerate if there are changes

### 4. Implement Real Component

```tsx
// my-component.tsx
export function MyComponent({ ... }) {
  // Complete implementation
}
```

### 5. Update Story

```tsx
// my-component.stories.tsx
import { MyComponent } from './my-component'

// Remove wireframe, import real component
```

## Identifying Wireframes vs Real Components

### Labeling Convention

Wireframes include a comment at the top:

```tsx
/**
 * Wireframe generated from: specs/04-interaction/views/UI-XXX.md
 * Status: draft (pending real implementation)
 *
 * TODO: Replace wireframe with real implementation
 */
```

### Quick Verification

- **Is the component defined inside the `.stories.tsx`?** → It is a wireframe
- **Is the component imported from a separate `.tsx`?** → It is a real component

## Summary

| Phase | File | Content | Purpose |
|-------|------|---------|---------|
| Specification | `.md` in /specs | Formal definition | What it should do |
| Wireframe | `.stories.tsx` | Embedded component | How it looks |
| Implementation | `.tsx` + updated `.stories.tsx` | Real component | Production code |

The value of this workflow is that it allows you to validate the design visually before investing in implementation, always maintaining traceability back to the original specifications.

## Automatic Spec → Story Synchronization

### The Desynchronization Problem

When specs evolve, stories can become outdated:

```
Week 1: Spec has 3 states → Story has 3 stories ✓
Week 4: Spec has 5 states → Story has 3 stories ✗
```

### Solution: Pre-commit Hook + Claude

The project includes an automatic synchronization system:

```
┌─────────────────────────────────────────────────────────────────┐
│  SYNCHRONIZATION FLOW                                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. Developer edits UI spec                                     │
│         │                                                       │
│         ▼                                                       │
│  2. git add specs/04-interaction/views/UI-*.md                  │
│         │                                                       │
│         ▼                                                       │
│  3. git commit                                                  │
│         │                                                       │
│         ▼                                                       │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  Pre-commit hook detects modified UI specs              │   │
│  │  └── Warns if no stories are in staging                 │   │
│  └─────────────────────────────────────────────────────────┘   │
│         │                                                       │
│         ▼                                                       │
│  4. Developer runs: claude "/sync-story auto"                   │
│         │                                                       │
│         ▼                                                       │
│  5. Claude synchronizes the stories automatically               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Available Commands

| Command | Description |
|---------|-------------|
| `/generate-story {spec}` | Creates a new story from a spec |
| `/sync-story {spec}` | Updates an existing story |
| `/sync-story auto` | Synchronizes all modified UI specs |

### Story Structure with Zones

Generated stories have two zones:

```tsx
// ============================================
// @generated from specs/04-interaction/views/UI-*.md
// DO NOT EDIT - Automatically regenerated
// ============================================

// ... code generated from the spec ...

// ============================================
// @custom - Manual extensions (NOT overwritten)
// ============================================

// ... custom code that is preserved ...
```

### Using the Pre-commit Hook

```bash
# The hook runs automatically when committing
git commit -m "feat: update ProductForm spec"

# Hook output:
# 🔍 Checking modified UI specs...
# 📋 Modified UI specs detected:
#    • specs/04-interaction/views/UI-ProductForm.md
#
# ⚠️  WARNING: You have modified UI specs but no stories are in staging.
#    Options:
#    1. Run: claude "/sync-story auto" to synchronize
#    2. Continue without synchronizing

# To synchronize
claude "/sync-story auto"

# To skip verification (not recommended)
git commit --no-verify -m "..."
```

### Advantages of This Approach

| Aspect | Benefit |
|--------|---------|
| **Intelligence** | Claude interprets the spec, does not use rigid templates |
| **Flexibility** | @custom section is always preserved |
| **Synchronization** | Impossible to forget updating stories |
| **Low effort** | Only edit the spec, Claude does the rest |

### Configuration

The relevant files are:

- `.husky/pre-commit` - Hook that detects changes
- `.claude/commands/sync-story.md` - Synchronization command
- `scripts/detect-ui-spec-changes.ts` - Detection script
