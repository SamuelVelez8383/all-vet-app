# AllVet App - Documentation

Living documentation for the AllVet expense/income tracker.

## Structure

```
docs/
├── README.md              ← You are here
├── 01-prd.md              ← Product Requirements Document
├── 02-design-system.md    ← Visual language and components
├── 03-ui-wireframes.md    ← Screen layouts and flows
├── 04-architecture.md     ← Tech stack, layers, diagrams
├── 05-data-model.md       ← Entities, relationships, schema
├── 06-api-design.md       ← Interface contracts between layers
├── 07-implementation.md   ← Build order and milestones
├── decisions/             ← Architecture Decision Records (ADRs)
└── learnings/             ← Personal learnings and notes
```

## Process

We work through each step sequentially. Each one is a conversation — we discuss, refine, then lock it in before moving to the next. Steps with sub-steps are committed section by section.

## Progress

- [ ] Step 1: PRD → `01-prd.md`
  - [x] 1.1 Problem & Vision
  - [x] 1.2 Target Users
  - [x] 1.3 Goals & Success Metrics
  - [x] 1.4 Competitive Analysis (existing tools, APIs, inspiration)
  - [x] 1.5 Scope (MVP → v1.0 → stretch → parked)
  - [ ] 1.6 User Stories
  - [ ] 1.7 Constraints & Assumptions
  - [ ] 1.8 Agent Role Rules → `.cursor/rules/`
    - 1 orchestrator rule (phase-aware, activates the right role per step)
    - 4 role rules: `pm.mdc`, `architect.mdc`, `designer.mdc`, `engineer.mdc`
- [ ] Step 2: Design System → `02-design-system.md`
- [ ] Step 3: UI Wireframes → `03-ui-wireframes.md`
- [ ] Step 4: Architecture → `04-architecture.md`
- [ ] Step 5: Data Model → `05-data-model.md`
- [ ] Step 6: API/Interface Design → `06-api-design.md`
- [ ] Step 7: Implementation Plan → `07-implementation.md`
- [ ] Step 8: Build MVP

**Current → Step 1.6: User Stories**
