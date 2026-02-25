---
# @file-pattern: ^PRD-.+\.md$
# @path-pattern: 00-requirements/

id: PRD-Name                  # @required @pattern: ^PRD-.+
kind: prd                     # @required
status: draft                 # @required @enum: draft|review|approved|deprecated|superseded
---

# PRD: Epic Name <!-- required -->

## Problem / Opportunity <!-- required -->

Clear description of the problem being solved or the opportunity being seized.

- What pain does the user have?
- What business opportunity exists?
- Why is it important now?

## Users and Jobs-to-be-done <!-- required -->

| Persona | Job-to-be-done | Frequency |
|---------|----------------|-----------|
| User type A | Wants to achieve X to obtain Y | Daily |
| User type B | Needs to do Z because W | Weekly |

## Scope <!-- required -->

### In Scope

- Functionality 1
- Functionality 2
- Functionality 3

### Out of Scope

- What is NOT included in this PRD
- Functionality deferred to future
- Integrations not considered

## Functional Requirements <!-- optional -->

### Use Cases

- [[UC-NNN-Name]] - Brief description
- [[UC-MMM-Name]] - Brief description

### Business Rules

- [[BR-XXX-NNN]] - Brief description
- [[BR-YYY-NNN]] - Brief description

## Non-Functional Requirements <!-- optional -->

- [[NFR-Performance]] - P95 < 500ms
- [[NFR-Security]] - Authentication required
- Compliance: GDPR, WCAG 2.1 accessibility

## Success Metrics <!-- optional -->

| Metric | Baseline | Target | How measured |
|--------|----------|--------|--------------|
| Conversion | 10% | 15% | Analytics event X |
| Time on task | 5 min | 2 min | UI timer |
| Feature NPS | - | > 50 | Post-use survey |

## Dependencies <!-- optional -->

- **APIs**: [[API-XXX]] - Description
- **Events**: [[EVT-XXX]] - Description
- **Teams**: Team Y for integration Z
- **Infrastructure**: Infra requirements

## Acceptance Criteria / Go-Live <!-- required -->

- [ ] All scenarios [[SCN-XXX]] passing
- [ ] Test coverage > 80%
- [ ] Performance validated in staging
- [ ] User documentation updated
- [ ] Rollback plan defined

## Risks and Mitigations <!-- optional -->

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Risk 1 | High | High | Mitigation plan |
| Risk 2 | Medium | Medium | Alternative plan |

## Timeline and Milestones <!-- optional -->

> Note: Do not include specific dates, only phases/milestones

- **Phase 1**: MVP with core functionality
- **Phase 2**: Improvements based on feedback
- **Phase 3**: Optimizations and scale
