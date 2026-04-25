---
name: Feature Request / RFC
about: Propose a new feature or significant change to the LocalPulse Payment Gateway™
title: '[RFC] '
labels: ['enhancement', 'rfc', 'triage-needed']
assignees: ''
---

> **Note:** This template is for internal engineering proposals only. External feature requests should be submitted via the [support portal](https://support.localpulse.pro) or by [scheduling a consultation](https://cal.com/ciprian-stefan-plesca).

---

## Feature Summary

<!-- A one-sentence description of the proposed feature or change -->

## Problem Statement

<!-- What problem does this solve? What is the current pain point or gap? Link to any customer requests, support tickets, or business objectives that motivate this. -->

## Proposed Solution

<!-- Describe the solution you'd like. Be as specific as possible. -->

## Alternative Approaches Considered

<!-- What other approaches did you consider? Why were they rejected in favor of the proposed solution? -->

## API / Interface Design

<!-- If this involves API changes, sketch the proposed API design -->

```typescript
// Example: New endpoint or interface design

POST /v2/your-new-endpoint
{
  "param": "value"
}

Response:
{
  "id": "obj_xxx",
  ...
}
```

## Security Considerations

<!-- How does this feature affect security? Consider: authentication, authorization, data exposure, injection risks, PCI DSS scope -->

- **PCI DSS impact:** <!-- Does this touch the CDE? -->
- **Authentication changes:** <!-- Does this require new auth flows? -->
- **Data sensitivity:** <!-- Does this handle sensitive data? -->

## Breaking Changes

<!-- Does this require breaking changes to existing APIs or behavior? If yes, describe the migration path -->

- [ ] No breaking changes
- [ ] Breaking change — migration path: ____________

## Performance Impact

<!-- Expected impact on latency, throughput, or resource usage. Include any benchmarks or estimates. -->

## Rollout Strategy

<!-- How should this be rolled out? Feature flag? Gradual rollout? Full release? -->

## Success Metrics

<!-- How will we know this feature is successful? -->

## Effort Estimate

- [ ] Small (< 1 week)
- [ ] Medium (1–3 weeks)
- [ ] Large (1–2 months)
- [ ] XL (> 2 months — consider breaking into phases)

## Dependencies & Blockers

<!-- Does this depend on other features, infrastructure changes, or third-party capabilities? -->

---

<!-- © 2026 Ciprian Stefan Plesca · LocalPulse Payment Gateway™ -->
