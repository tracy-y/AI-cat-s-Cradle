# AGENTS.md - Codex Implementation Contract

## Mission
Build the AI Cat's Cradle application described in this repository. Work incrementally and keep the system testable. Do not attempt the whole application in one change.

## Mandatory architecture rules
1. The canonical game/topology state lives in testable Python domain modules, not in TouchDesigner operators.
2. TouchDesigner owns capture/rendering/orchestration but consumes canonical state.
3. MVP interaction is deterministic and state-aware. Do not introduce full rope physics as the source of game truth.
4. Pattern definitions and thresholds should be externalized in validated configuration rather than hard-coded throughout the TD network.
5. Every topology mutation must be deterministic and validated.
6. Camera loss, invalid gestures, and reset must never silently corrupt topology.
7. Raw camera video must not be persisted by default. Landmark-session recording for deterministic testing is encouraged.

## Work sequence
Implement in this order unless an ADR explains why it must change:
- E0 Repository/Foundation
- E1 Tracking
- E2 Calibration
- E3 String Renderer
- E4 Interaction Engine
- E5 Topology/FSM
- E6 End-to-End Integration
- E7 Virtual Opponent
- E8 UX/Feedback
- E9 QA/UAT/PVT
- E10 Release

## Per-task workflow
For every epic/task:
1. Read README and relevant docs first.
2. State assumptions and acceptance criteria before implementation.
3. Make the smallest coherent implementation that meets the acceptance criteria.
4. Add/update tests in the same change.
5. Run all available automated tests and report exact results.
6. Update documentation/configuration examples affected by the change.
7. For TouchDesigner behavior that cannot be CI-tested, write a reproducible manual verification procedure and expected evidence.
8. Do not mark work complete when its exit gate has not been demonstrated.
9. Record significant architecture changes under `docs/adr/`.

## Definition of Done
- Acceptance criteria demonstrated.
- Unit/integration tests added or updated and passing.
- Schema/pattern validation passes.
- Relevant documentation is current.
- Debug/telemetry behavior is adequate to diagnose failure.
- No known P0 regression introduced.
- Manual TD verification steps exist where automation is infeasible.

## Git conventions
- `main` must remain releasable.
- Feature branches: `feature/<ticket>-<short-name>`.
- Fix branches: `fix/<ticket>-<short-name>`.
- Prefer coherent commits with descriptive messages.
- PRs should include requirement/test mapping and visual evidence for visual changes.

## Initial milestone restriction
M1 stops at: live camera -> two-hand landmarks -> calibrated/smoothed coordinates -> selected finger anchors -> rendered virtual loop/string -> debug overlay -> deterministic landmark record/replay fixture.

Do not implement complete Cat's Cradle gameplay before M1 is stable.
