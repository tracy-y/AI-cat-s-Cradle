# Development Backlog

This backlog defines the recommended execution order for Codex.

| Epic | Deliverables | Dependencies | Exit gate |
|---|---|---|---|
| E0 Foundation | Python package skeleton, config/schema strategy, logging, CI baseline, TD shell, docs | None | CI green; shell starts; camera preview path defined |
| E1 Tracking | Camera/tracker adapter, normalized landmark frames, stable IDs, debug overlay, recorder/replayer | E0 | Two-hand fixture can be captured and replayed |
| E2 Calibration | Coordinate mapper, smoothing, confidence/loss behavior | E1 | Known hand positions map within agreed tolerance |
| E3 String Renderer | Initial topology fixture, geometry builder, TouchDesigner string renderer | E0,E2 | Virtual loop follows selected anchors continuously |
| E4 Interaction | Segment math, hover, engage, attach, pull, release, debounce | E2,E3 | Recorded fixture yields deterministic interaction events |
| E5 Topology/FSM | Canonical graph, mutation commands, validator, pattern files | E0 | Chosen transition sequence passes domain tests |
| E6 Integration | Event -> FSM -> topology -> geometry pipeline | E4,E5 | First valid human transition works end-to-end |
| E7 Opponent | Scripted AI transition, animation, turn orchestration | E6 | Human -> AI -> human loop repeats reliably |
| E8 UX | Hints, turn indicator, success/failure feedback, reset/recalibrate | E7 | User can play without developer console |
| E9 QA | Regression/replay/system suite, UAT and PVT scripts/evidence templates | All | P0 suite green and UAT ready |
| E10 Release | Release candidate, runbook, known issues, rollback, tag | E9 | UAT + PVT gates satisfied |

## First Codex task

Start with **E0 Foundation only**.

Expected E0 output:
1. Inspect repository docs and state assumptions.
2. Propose the minimal Python/TouchDesigner integration approach for M1.
3. Create source/test/config/schema directories required by M1 only.
4. Add Python project/dependency configuration appropriate for the chosen tracker approach.
5. Add a minimal CI workflow for Python checks/tests/schema validation.
6. Add structured logging/config loader skeletons.
7. Add placeholder interfaces/data classes for normalized landmark frames; do not prematurely implement topology gameplay.
8. Add unit tests for any executable foundation code.
9. Document how to launch the tracking skeleton and how TouchDesigner will consume landmark data.
10. Report test commands/results and remaining prerequisites (including TouchDesigner/manual setup).

Do not start E1/E2 implementation until E0's exit gate is met.
