# Feature Discovery: From Idea to KDD Artifacts

## Introduction

This document describes the conversational process to capture, refine, and formalize new features or improvements in the system. The goal is to reduce initial friction while maintaining KDD rigor.

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│   RAW IDEA  →  REFINEMENT  →  SYNTHESIS  →  KDD ARTIFACTS      │
│   (lightweight)  (conversation)  (proposal)   (formal)          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Phase 1: Lightweight Capture

### Where ideas live

```
/specs/00-inbox/
├── IDEA-2024-12-13-pause-cart.md
├── IDEA-2024-12-15-detail-levels.md
└── _archive/
    └── (already processed ideas)
```

### How to capture an idea

**Option A: `/feature` command**
```bash
# In Claude Code
/feature
```
Claude will guide you to capture the idea and begin refinement.

**Option B: Manual file**
1. Create a file in `/specs/00-inbox/` using the `idea.template.md` template
2. Fill in at least the "The Problem" and "The Idea" sections
3. Start a conversation with Claude to refine

### What to include in the initial capture

| Section | Required | Description |
|---------|----------|-------------|
| The Problem | Yes | What current situation is problematic? |
| The Idea | Yes | What is your proposed solution? |
| Examples | Recommended | A concrete use case |
| Questions | Optional | Doubts or aspects to discuss |

**Don't worry about:**
- Using exact KDD terminology
- Identifying entities or rules
- Perfect formatting

We refine that in the conversation.

---

## Phase 2: Conversational Refinement

### The exploration process

Claude follows a structured flow of questions to fully understand the idea:

```
┌─────────────────────────────────────────────────────────────────┐
│                    EXPLORATION FLOW                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. PROBLEM CLARIFICATION                                       │
│     ├── What specific problem does it solve?                    │
│     ├── Who has this problem?                                   │
│     ├── How often does it occur?                                │
│     └── What happens today when it occurs?                      │
│                                                                 │
│  2. ENTITY IDENTIFICATION                                       │
│     ├── What existing entities are involved?                    │
│     ├── Do we need a new entity?                                │
│     └── Are there state changes in entities?                    │
│                                                                 │
│  3. BUSINESS RULES                                              │
│     ├── Are there constraints to meet?                          │
│     ├── What should NOT happen?                                 │
│     └── Edge cases of concern?                                  │
│                                                                 │
│  4. USER INTERACTION                                            │
│     ├── How does the user trigger it?                           │
│     ├── What do they see before/during/after?                   │
│     └── What feedback do they need?                             │
│                                                                 │
│  5. VERIFICATION                                                │
│     ├── How do we know it works?                                │
│     ├── Example of the "happy" scenario                         │
│     └── Example of a case that should fail                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Conversation example

```
User: I want the customer to be able to pause a Cart midway
      and resume it later.

Claude: Got it. Let's explore this idea.

        **About the problem:**
        What happens currently when a customer is interrupted
        midway through a Cart? Is all progress lost?

User: Yes, they have to start from scratch. It's frustrating because
      sometimes we already have 3 items added.

Claude: Of course, that's a real pain point.

        **About the constraints:**
        Should there be a time limit to resume?
        For example, does a cart paused for 1 week
        still make sense?

User: Good question. I'd say 24-48 hours max,
      after that it loses context.

Claude: Perfect. That would be a business rule: BR-CART-PAUSE.

        **About the interaction:**
        Where would you expect to see the "Pause" button?
        And how would you return to the paused cart?

[... refinement continues ...]
```

---

## Phase 3: Synthesis

### The intermediate deliverable

Before creating artifacts, Claude produces a **structured summary** that maps the idea to KDD components:

```markdown
## Summary: Pause and Resume Cart

### Domain Impact (02-domain/)
| Type | Artifact | Action |
|------|----------|--------|
| Entity | [[Cart]] | Modify: new state `paused` |
| Event | EVT-Cart-Paused | Create |
| Event | EVT-Cart-Resumed | Create |
| Rule | BR-CART-PAUSE | Create: 24h timeout |

### Capabilities (03-capabilities/)
| Type | Artifact | Action |
|------|----------|--------|
| Command | CMD-PauseCart | Create |
| Command | CMD-ResumeCart | Create |

### Interaction (04-interaction/)
| Type | Artifact | Action |
|------|----------|--------|
| Use Case | UC-XXX-Pause-Cart | Create |
| Use Case | UC-XXX-Resume-Cart | Create |
| View | UI-CartActive | Modify: add button |

### Verification (05-verification/)
| Type | Artifact | Action |
|------|----------|--------|
| Criteria | REQ-XXX-Pause-Cart | Create |
| Example | Pause with completed items | Create |
| Example | Resume before timeout | Create |
| Example | Timeout expired | Create |

### Example Scenarios
```gherkin
Scenario: Successfully pause cart
  Given an active Cart with 2 completed CartItems
  When the Customer pauses the Cart
  Then the Cart transitions to "paused" state
  And the pause date/time is recorded
  And a CartPaused event is generated

Scenario: Paused cart timeout
  Given a Cart paused for more than 24 hours
  When the system checks paused carts
  Then the Cart transitions to "cancelled" state
  And the Customer is notified
```
```

### Validation before proceeding

Claude confirms with the user:

```
✓ Does this summary capture your intent?
✓ Are the example scenarios correct?
✓ Is there any case we missed?
✓ Shall I proceed to create the KDD artifacts?
```

---

## Phase 4: Artifact Generation

### What gets generated

With user approval, Claude:

1. **Modifies** existing artifacts (e.g.: add state to Cart)
2. **Creates** new artifacts using templates from `/kdd/templates/`
3. **Links** everything correctly in the KDD graph
4. **Updates** the original idea to `processed` status

### Generation order

```
1. Domain (foundations)
   └── Entities → Events → Rules

2. Capabilities (what it can do)
   └── Commands → Queries → Processes

3. Interaction (how it's used)
   └── Use Cases → Views

4. Verification (how it's tested)
   └── Criteria → BDD Examples
```

### Automatic validation

After generating, run:

```bash
bun run validate:specs
```

To verify:
- Valid front-matter
- No broken links
- Complete templates

---

## Best Practices

### For whoever captures the idea

1. **Be specific about the problem** - "Users complain" is vague; "We lose the work of 3 items when the session drops" is concrete
2. **Include a real example** - It helps tremendously to understand the context
3. **Don't self-censor** - "Bad ideas" sometimes lead to good solutions

### For the refinement process

1. **One idea = one problem** - If there are multiple problems, separate them
2. **Validate assumptions** - "I assume X, is that correct?"
3. **Think about edge cases** - What happens if...?

### For generation

1. **Review before approving** - The summary is your last chance to adjust
2. **Not everything needs all artifacts** - A small idea may be just 1-2 documents
3. **Iterate** - If after generating you see something wrong, adjust it

---

## Useful Commands

```bash
# Start feature discovery flow
/feature

# Validate specifications after generating
bun run validate:specs

# View pending ideas to process
ls specs/00-inbox/*.md | grep -v README
```

---

## FAQ

### Can I skip the process and create artifacts directly?

Yes, if you have total clarity on what you need. The conversational process is for when the idea is still fuzzy.

### What happens with ideas that don't get implemented?

They can stay in `00-inbox/` with `raw` status or be moved to `_archive/` with a note about why they were discarded.

### How long should refinement take?

It depends on the complexity. A simple idea can take 5-10 minutes. A complex feature may require multiple sessions.

### Can I refine without Claude?

Yes, the process is documented here. But Claude helps to not forget important aspects and to maintain consistency with the existing domain.
