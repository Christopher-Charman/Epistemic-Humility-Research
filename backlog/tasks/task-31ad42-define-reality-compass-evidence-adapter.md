---
id: task-31ad42
title: Define Reality Compass evidence adapter contract
status: in-progress
assignee:
- '@chatgpt'
tier: P
priority: high
experiment: ''
component: docs/architecture
depends_on: []
files: []
new_files:
- docs/architecture/reality-compass-evidence-adapter.md
blocker: ''
created_date: '2026-10-05'
updated_date: '2026-10-05'
---
## Description

Define the design-only interoperability contract for exposing governed EHR experiment evidence and model-internal epistemic readouts to Reality Compass without collapsing answerability, correctness, stated confidence, trust, authority, confidence, or truth.

## Acceptance Criteria
- [x] Preserve EHR signed experiment and machine lifecycle authority without rewriting upstream evidence.
- [x] Define typed experiment and model-internal observation records with explicit provenance.
- [x] Define required non-inferences and semantic collision handling, including EHR "trust" versus Reality Compass TRUST.
- [x] Define dependence, calibration, portability, routing, concurrency, and read-only implementation boundaries.
- [x] Define a falsifiable acceptance battery for any future executable adapter.
- [x] Validate the repository task/index gates after the design specification is added.

## Work Log
- 2026-10-05: Read-only transfer audit completed against the fork and current Reality Compass state.
- 2026-10-05: Added design-only adapter contract. No signed experiment, runtime, or Reality Compass core semantic was modified.
