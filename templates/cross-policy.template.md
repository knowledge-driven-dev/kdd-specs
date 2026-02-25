---
# @file-pattern: ^XP-.+\.md$
# @path-pattern: 02-behavior/policies/

id: XP-{DOMAIN}-NNN            # @required @pattern: ^XP-[A-Z]+-\d{3}$
kind: cross-policy             # @required
status: draft                  # @required @enum: draft|review|approved|deprecated|superseded
---

# XP-{DOMAIN}-NNN: {Title}

## Purpose <!-- required -->

Description of the cross-cutting concern this policy addresses. Explain why this logic needs to be applied transversally instead of repeating it in each command.

## Statement <!-- required -->

Clear statement of the policy in natural language. Affected domain entities should be mentioned with wiki-links: [[Entity1]], [[Entity2]].

## EARS Formalization <!-- required -->

```
BEFORE executing any command with {attribute}={value},
the system SHALL {verification}
  AND SHALL {action if OK}
  AND SHALL reject with error "{ERROR_CODE}" if {rejection condition}.

AFTER a command completes successfully,
the system SHALL {post-success action}.

AFTER a command fails,
the system SHALL {post-failure action}.
```

## Examples <!-- required -->

### Successful Verification
- Initial condition → verification passes → command executes → result

### Failed Verification
- Initial condition → verification fails → error returned

## Standard Behavior <!-- required -->

### Verification (BEFORE)
What is verified before executing the command.

### Success (AFTER)
What happens after the command completes successfully.

### Rejection
What happens if verification fails. Include error code and standard message.

### Rollback
What happens if the command fails after passing verification.
