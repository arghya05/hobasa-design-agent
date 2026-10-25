---
name: design-steps
description: Procedures, inputs, outputs and gates for every Design agent step in the Hobasa agentic SDLC. Use when running any DES-01, DES-02, DES-03, DES-04 step.
---

# Design agent steps

## DES-01 Platform Architecture Update ¹

- Trigger: TRD Completion
- Action: If the feature needs an architectural change at the platform level,so firstly it gets updated in the platform architectural level document
- Outcome: Update in the platform architecture
- Reads: feat.trd, org.architecture
- Writes: feat.platform_update
- Gate: automated checks; sampled human evaluation; runs only when only when the TRD flags a platform-level change

## DES-02 Design Document

- Trigger: TRD Completion
- Action: Creating the architecture design document with HLD & LLD
- Outcome: Architecture Design document with HLD and LLD
- Reads: feat.prd, feat.prototype, feat.trd, org.architecture, org.data_model, org.design_library
- Writes: feat.design, feat.screens
- Gate: Architect, UX lead

## DES-03 Understand HLD or LLD+ Doubts Clarifications

- Trigger: Task creation
- Action: Not defined in workbook; complete the readiness action
- Outcome: Assumptions, Questions or Clarifications would be looped till the developer has achieved complete clarity
- Reads: feat.design, feat.stories, feat.trd
- Writes: feat.clarifications
- Gate: automated checks; sampled human evaluation

## DES-04 Task Creation

- Trigger: design document with HLD & LLD
- Action: Not defined in workbook; complete the readiness action
- Outcome: Committed user story
Task creation for actual engineering effort for each committed user story
- Reads: feat.stories, feat.design, feat.clarifications, org.risk_map
- Writes: feat.tasks, feat.tier
- Gate: Developer

