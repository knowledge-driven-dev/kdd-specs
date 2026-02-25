# Layer 03: Experience

## The Presentation Layer: How Do Users See It?

---

## Introduction

The Experience layer answers the question: **How is the system presented to the user?**

In the previous layers we defined the domain (01-Domain) and the available operations including use cases (02-Behavior). The Experience layer is where we **design the visual interface** that implements those operations.

> **Important note**: This layer is the **PRESENTATION** layer. It references 02-Behavior and contains only view (Views) specifications.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   00-Requirements  →  01-Domain   →  02-Behavior     →  03-Experience       │
│                                                                              │
│   "Why does            "What           "How does           "HOW DO           │
│    it exist?"          exists?"         it behave?"         USERS SEE IT?"  │
│                                                                              │
│   ──────────────────────────────────────────────────────────────────────────│
│                                                                              │
│   Motivation          Conceptual      Functional          PRESENTATION      │
│   Context             (entities)      (operations)        (views)           │
│   Objectives          (rules)         (use cases)                           │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Philosophy: Views as Consumers of Behavior

### The User as a Consumer of Behavior

The design of this layer starts from a key principle:

> **User interfaces are "clients" of the system's capabilities, not their definition.**

This means that a View (UI) **does not define** what the system does; a View **consumes** what the system already knows how to do.

```
❌ Incorrect: "The system can place orders because there is a form"
✅ Correct:   "There is a form because the system can place orders"
```

This distinction is crucial because:

1. **Decouples logic from presentation**: The same Command can be invoked from a web app, a CLI, an API, or an automated test.
2. **Enables independent evolution**: You can completely redesign the UI without touching the business logic.
3. **Facilitates testing**: Commands can be tested in isolation from the UI.

### The Three Responsibilities of a View

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   1. PRESENT DATA                                                            │
│      Retrieve information via Queries and display it to the user             │
│      Query → Data → Visual rendering                                         │
│                                                                              │
│   2. CAPTURE INTENTS                                                         │
│      Translate user actions into Commands                                    │
│      Click/Input → Validation → Command                                      │
│                                                                              │
│   3. PROVIDE FEEDBACK                                                        │
│      Communicate the result of operations                                    │
│      Loading → Success/Error → Visual update                                 │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

The UI **must not contain business logic**. If you find yourself writing complex validation rules or calculations in a component, that logic probably belongs in a Command or a Domain Service.

---

## The Single Artifact: Views

Views are **visual interface specifications** that implement the Use Cases defined in 02-Behavior.

### Types of Views

| Type | Prefix | Purpose | Has route |
|------|--------|---------|-----------|
| **View** | `UI-{Name}` | Full page | Yes (`/orders/:id/edit`) |
| **Component** | `UI-{Name}` | Reusable piece | No |
| **Modal** | `UI-{Name}Modal` | Overlay on view | No |

### Structure of a View

```markdown
---
id: UI-OrderEditor
kind: view
title: Order Editor
route: /orders/:id/edit
status: approved
---

# UI-OrderEditor

## Context
| Element | Description |
|---------|-------------|
| Route | `/orders/:id/edit` |
| Type | Full page |
| Access | Authenticated customer |
| Use Case | [[UC-001-PlaceOrder]], [[UC-002-EditOrder]] |

## Queries Consumed
- [[QRY-001-GetOrder]] - Loads order data

## Commands Invoked
- [[CMD-001-PlaceOrder]] - When creating new
- [[CMD-002-UpdateOrder]] - When saving changes

## Layout

```ascii
┌──────────────────────────────────────────────────────────────────────────────┐
│  HEADER                                                                       │
│  ← Back    [Order Title]                            [Save] [Next →]          │
├────────────────┬─────────────────────────────────────────────┬───────────────┤
│                │                                             │               │
│  NAV STEPPER   │            MARKDOWN EDITOR                  │  TABLE OF     │
│  (200px)       │            (flexible)                       │  CONTENTS     │
│                │                                             │  (220px)      │
│  ○ Objectives  │  # Order Objectives                         │               │
│  ○ Context     │                                             │  • Objectives │
│  ● Details     │  Describe the main objectives               │  • Context    │
│                │  here...                                     │  • Details    │
│                │                                             │               │
├────────────────┴─────────────────────────────────────────────┴───────────────┤
│  FOOTER                                                                       │
│  [Enhance with AI]           Last saved: 2 min ago            [Preview]      │
└──────────────────────────────────────────────────────────────────────────────┘
```

## States

### Loading
- Show editor skeleton
- Header with placeholder title

### Empty (New Order)
- Empty form with guiding placeholders
- Stepper at first step

### Success (Existing Order)
- Data loaded in the editor
- Stepper reflects progress

### Error
- Toast with error message
- Option to retry loading

## Interactions

### Click on [Save]
- **Trigger**: Click on Save button
- **Precondition**: Valid form (title not empty)
- **Command**: [[CMD-002-UpdateOrder]]
- **Feedback**:
  - During: Spinner + disabled button
  - Success: Toast "Changes saved"
  - Error: Toast with error message

### Click on [Next →]
- **Trigger**: Click on Next button
- **Precondition**: Order saved
- **Navigates to**: [[UI-ConfigureProducts]]

## Responsive

| Breakpoint | Changes |
|------------|---------|
| Desktop (>1024px) | 3-column layout |
| Tablet (768-1024px) | Collapsible TOC, 2 columns |
| Mobile (<768px) | Single column, horizontal stepper |

## Accessibility
- Focus trap in modals
- Keyboard shortcuts: Ctrl+S to save
- ARIA labels on action buttons
```

---

## The Unidirectional Dependency Principle

This is the heart of how KDD structures relationships between layers:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   FUNDAMENTAL RULE:                                                          │
│                                                                              │
│   03-Experience → references → 02-Behavior                                   │
│   02-Behavior → DOES NOT reference → 03-Experience                           │
│                                                                              │
│   The View knows which Commands it can invoke.                               │
│   The Command DOES NOT know which Views use it.                              │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Why This Rule

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   The same CMD-001-PlaceOrder can be invoked from:                           │
│                                                                              │
│   ┌─────────────────┐                                                        │
│   │  UI-OrderEditor │ ────┐                                                  │
│   └─────────────────┘     │                                                  │
│                           │                                                  │
│   ┌─────────────────┐     │      ┌─────────────────────┐                    │
│   │  REST API       │ ────┼────► │ CMD-001             │                    │
│   └─────────────────┘     │      │ PlaceOrder          │                    │
│                           │      │                     │                    │
│   ┌─────────────────┐     │      │ (does not know who  │                    │
│   │  Admin CLI      │ ────┤      │  invokes it)        │                    │
│   └─────────────────┘     │      └─────────────────────┘                    │
│                           │                                                  │
│   ┌─────────────────┐     │                                                  │
│   │  E2E Test       │ ────┘                                                  │
│   └─────────────────┘                                                        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Implications in Specifications

```yaml
# ✅ CORRECT: The View knows the Command
# UI-OrderEditor.md
## Commands Invoked
- [[CMD-001-PlaceOrder]]

# ❌ INCORRECT: The Command knows the View
# CMD-001-PlaceOrder.md (in 02-behavior)
## Used by:  # SHOULD NOT EXIST
- [[UI-OrderEditor]]
```

---

## States of a View

Every view must specify its possible states:

### 1. Loading

While initial data is being loaded.

```ascii
┌──────────────────────────────────────┐
│  ← Back    ████████████              │
├──────────────────────────────────────┤
│                                      │
│      ┌────────────────────┐          │
│      │   ▓▓▓▓▓▓▓▓▓▓▓▓▓   │          │
│      │   ▓▓▓▓▓▓▓▓▓▓▓▓▓   │          │
│      │   ▓▓▓▓▓▓▓▓▓▓▓▓▓   │          │
│      └────────────────────┘          │
│          (Skeleton)                  │
└──────────────────────────────────────┘
```

### 2. Empty

When there is no data to display.

```ascii
┌──────────────────────────────────────┐
│                                      │
│         📋                           │
│                                      │
│    You don't have any orders yet     │
│                                      │
│    Create your first order to        │
│    start processing items            │
│                                      │
│       [+ Create my first order]      │
│                                      │
└──────────────────────────────────────┘
```

### 3. Error

When something fails.

```ascii
┌──────────────────────────────────────┐
│                                      │
│         ⚠️                           │
│                                      │
│    We couldn't load the data         │
│                                      │
│    Error: Connection timeout         │
│                                      │
│       [Retry]  [Go Back]            │
│                                      │
└──────────────────────────────────────┘
```

### 4. Success/Default

The normal state with loaded data.

---

## Interaction Structure

The most critical section of a View specification. It documents exactly what happens when the user does something.

### Fields of an Interaction

| Field | Description | Example |
|-------|-------------|---------|
| **Trigger** | What user action triggers this | Click, Hover, Submit |
| **Precondition** | Conditions that must be met | Valid form |
| **Command** | Command that is executed | CMD-001-PlaceOrder |
| **Query** | Query that is executed | QRY-001-GetOrder |
| **Feedback** | Visual feedback to the user | Spinner, Toast |
| **Opens** | Modal/drawer that opens | UI-ProductModal |
| **Navigates to** | Destination view | UI-ConfigureProducts |

### Difference Between "Opens" and "Navigates to"

```
OPENS: Modal, drawer, popover (overlay on the current view)
─────
- The current view remains underneath
- The user can close and return
- Does not change the URL

NAVIGATES TO: Route/page change (replaces the current view)
─────────────
- The current view is unmounted
- Changes the browser URL
- The user uses "back" to return
```

---

## Connection with Storybook

View specifications are designed to generate Storybook stories:

```yaml
# In the front-matter of a View
storybook:
  category: "Views"
  auto-generate: true
```

Each documented **state** becomes a story:

| State in Spec | Generated Story |
|---------------|-----------------|
| Loading | `OrderEditor.stories.tsx` → `Loading` |
| Empty | `OrderEditor.stories.tsx` → `Empty` |
| Error | `OrderEditor.stories.tsx` → `Error` |
| Default | `OrderEditor.stories.tsx` → `Default` |

---

## Folder Structure

```
/specs/03-experience/
└── /views/
    ├── UI-Dashboard.md
    ├── UI-OrderEditor.md
    ├── UI-ConfigureProducts.md
    ├── UI-ActiveCart.md
    ├── UI-ViewAnalysis.md
    ├── UI-ProductCard.md          # Component
    ├── UI-ProductModal.md         # Modal
    └── UI-BillingBalance.md       # Component
```

---

## Checklist: Specifying a New View

When you create a new View specification, make sure to include:

- [ ] **ID** in `UI-{DescriptiveName}` format
- [ ] **kind**: `view`, `component`, or `modal`
- [ ] **Context**: Route (if applicable), type, required access
- [ ] **Use Cases** it implements (from 02-behavior)
- [ ] **Queries** it consumes
- [ ] **Commands** it invokes
- [ ] **Layout** with ASCII wireframe
- [ ] **States**: loading, empty, error, success
- [ ] **Interactions**: each user action documented
- [ ] **Responsive**: behavior at different breakpoints
- [ ] **Accessibility**: focus, keyboard nav, ARIA

---

## Anti-patterns to Avoid

### 1. Business Logic in the View

```typescript
// ❌ INCORRECT: Business rule in the component
function OrderEditor() {
  const canSave = order.products.length >= 3 && order.products.length <= 6
  // This rule should be in BR-PRODUCT-001 (01-domain)
}

// ✅ CORRECT: The UI only presents the result
function OrderEditor() {
  const { canInitiateCart } = useOrderValidation(order)
  // The validation comes from the domain
}
```

### 2. View that Defines Operations

```yaml
# ❌ INCORRECT: The View "invents" operations
# UI-OrderEditor.md
## Operations:
- Place order
- Duplicate order

# ✅ CORRECT: The View consumes existing Commands
# UI-OrderEditor.md
## Commands Invoked:
- [[CMD-001-PlaceOrder]]    # Already defined in 02-behavior
- [[CMD-004-DuplicateOrder]]
```

### 3. Specifying Only the Happy Path

```markdown
# ❌ INCORRECT: Only the happy case
## Interactions
- Click on Save → Order saved

# ✅ CORRECT: All cases
## Interactions

### Click on Save (success)
- Command: [[CMD-002-UpdateOrder]]
- Feedback: Toast "Changes saved"

### Click on Save (validation error)
- Feedback: Invalid fields highlighted

### Click on Save (server error)
- Feedback: Error toast with retry option
```

### 4. View without Use Case

```yaml
# ❌ INCORRECT: View without usage context
# UI-OrderEditor.md
(No mention of Use Cases)

# ✅ CORRECT: View linked to Use Cases
# UI-OrderEditor.md
## Context
| Use Case | [[UC-001-PlaceOrder]], [[UC-002-EditOrder]] |
```

---

## Summary

The Experience layer in KDD:

1. **Is presentational**: Only contains View specifications
2. **References backward**: Knows Commands/Queries/Use Cases from 02-behavior
3. **Is not referenced by previous layers**: Commands do not know which Views use them
4. **Is derivable**: If you have well-specified Use Cases, the UI is "just presentation"
5. **Is verifiable**: BDD tests exercise the UI against the Commands
6. **Generates Storybook**: Specifications feed the visual documentation

> **"The best UI is invisible: it presents the system's capabilities naturally, without inventing its own behavior."**

---

## Related Artifacts

- [[view.template]] - Template for views
- [[component.template]] - Template for components
- [[02-behavior]] - The previous layer: Behavior (Commands, Queries, Use Cases)
- [[04-verification]] - The next layer: Verification
- [[Introduction to KDD]] - KDD overview

---

*Last updated: 2025-01*
