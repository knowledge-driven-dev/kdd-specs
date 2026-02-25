---
# @file-pattern: ^PROC-\d{3}-.+\.md$
# @path-pattern: 02-behavior/processes/

id: PROC-NNN                  # @required @pattern: ^PROC-\d{3}$
kind: process                 # @required
status: draft                 # @required @enum: draft|review|approved|deprecated|superseded
---

# PROC-NNN: Process Name <!-- required pattern: ^PROC-\d{3}: -->

## Description <!-- optional -->

Process description:
- Process objective
- Actors involved
- Expected outcome

## Diagram <!-- required -->

<!-- expects: mermaid:flowchart -->
```mermaid
flowchart TD
    A[Start] --> B{Decision?}
    B -- Yes --> C[Task 1]
    B -- No --> D[Task 2]
    C --> E{Another decision?}
    D --> E
    E -- Continue --> F[Task 3]
    E -- Finish --> G[End]
    F --> G
```

## Participants <!-- optional -->

| Role | Responsibility |
|------|----------------|
| [[User]] | Initiates the process |
| System | Executes automatic tasks |
| [[OtherActor]] | Approves/validates |

## Steps <!-- optional -->

| # | Step | Actor | Input | Output | Rules |
|---|------|-------|-------|--------|-------|
| 1 | Description | User | Initial data | Result | [[BR-001-RuleName]] |
| 2 | Next step | System | Previous result | New state | - |
| 3 | Decision | User | Current state | Chosen option | [[BR-002-RuleName]] |

## Related Use Cases <!-- optional -->

- [[UC-NNN-Name]] - Step 1
- [[UC-MMM-Name]] - Steps 2-3

## Events Emitted <!-- optional -->

| Step | Event |
|------|-------|
| On start | [[EVT-Process-Started]] |
| On step 2 | [[EVT-Task-Completed]] |
| On finish | [[EVT-Process-Completed]] |

## Integration Points <!-- optional -->

- **External services**: APIs that are invoked
- **Notifications**: Emails, push, etc.
- **Persistence**: What is saved and when
