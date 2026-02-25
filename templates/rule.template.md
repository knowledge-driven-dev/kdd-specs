---
# @file-pattern: ^(BR|BP)-[A-Z]+-\d{3}\.md$
# @path-pattern: 01-domain/rules/

id: BR-ENTITY-NNN             # @required @pattern: ^(BR|BP)-[A-Z]+-\d{3}$
kind: business-rule           # @required @enum: business-rule|business-policy
status: draft                 # @required @enum: draft|review|approved|deprecated|superseded
---

# BR-ENTITY-NNN: RuleTitle <!-- required pattern: ^(BR|BP)-[A-Z]+-\d{3}: -->

## Statement <!-- required -->

Clear statement of the rule in natural language, understandable by non-technical stakeholders. Affected domain entities should be mentioned with wiki-links: [[Entity1]], [[Entity2]].

## Rationale <!-- required -->

Brief business reason. Explains the risk it prevents or the benefit it protects.

## When Applies <!-- required -->

Indicates at which points in the entity lifecycle the rule is evaluated (create, modify, state change).

## Violation Behavior <!-- required -->

Expected outcome when the rule fails. Example: user-visible error, blocked operation, disallowed state.

## Parameters (BP only) <!-- optional -->

If this is a Business Policy, list configurable parameters and their default values.

## Formalization <!-- optional -->

```
IF/WHEN/WHILE [condition],
the system SHALL [action]
  AND SHALL [additional action]
  AND SHALL NOT [prohibited action].
```

## Examples <!-- required -->

### Valid Cases
- Example of valid scenario
- Another valid scenario

### Invalid Cases
- Example of invalid scenario → expected behavior
- Another invalid scenario → expected behavior
