---
# @type: idea
# @description: Lightweight capture of an idea or feature before formalizing
# @file-pattern: ^IDEA-\d{4}-\d{2}-\d{2}-.+\.md$
# @path-pattern: 00-inbox/

status: raw                    # @enum: raw|exploring|ready|processed @default: raw
created: YYYY-MM-DD            # @required @format: date
author: "@username"            # @optional
priority: medium               # @enum: low|medium|high|critical @optional
tags: []                       # @type: array @optional
---

# Idea Title <!-- title-is-name -->

## The Problem <!-- required -->

<!-- Describe the problem or need that motivates this idea -->
<!-- Guiding questions:
     - What current situation is problematic?
     - Who has this problem?
     - How often does it occur?
     - What is the impact?
-->



## The Idea <!-- required -->

<!-- Describe your proposed solution in natural language -->
<!-- Don't worry about format, just capture the essence -->



## Examples <!-- optional but-recommended -->

<!-- Give a concrete example of how it would work -->
<!-- Example: "The user is in a session, clicks 'Pause',
     and can come back tomorrow to continue where they left off" -->



## Open Questions <!-- optional -->

<!-- Is there anything unclear or that needs to be discussed? -->

-

## Additional Context <!-- optional -->

<!-- Links, screenshots, relevant conversations, etc. -->


---

<!--
NEXT STEPS:
1. Save this file in /specs/00-inbox/
2. Use the /feature command in Claude Code
3. Claude will guide you to refine this idea
4. At the end, formal KDD artifacts will be generated
-->
