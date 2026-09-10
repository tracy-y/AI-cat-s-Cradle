# AI Cat's Cradle

A camera-driven, single-player virtual Cat's Cradle experience using TouchDesigner, real-time hand tracking, deterministic string topology, and a scripted virtual opponent.

## Product vision

The player's real hands are captured by a camera. Hand landmarks drive virtual string anchors in TouchDesigner. A state-aware interaction engine recognizes intentional hook/grab/pull/release actions, while a topology engine maintains the canonical string configuration. After a valid player move, a virtual opponent can perform the other player's transition so one person can play Cat's Cradle alone.

## MVP success criteria

- Track two hands and 21 landmarks per hand in real time.
- Map selected landmarks into TouchDesigner coordinates with calibration, smoothing, and confidence handling.
- Render a continuous virtual loop/string anchored to tracked fingers.
- Detect hover/intersection, hook/grab, pull, release, and commit gestures for scripted patterns.
- Represent anchors, segments, crossings, attachments, and over/under relationships as canonical data.
- Complete at least one end-to-end sequence: player turn -> validation -> topology update -> AI turn -> next state.
- Provide debug overlay, event logging, calibration/recalibration, and restart.
- Pass automated/component tests, integration/system tests, UAT, and PVT.

## Architecture

```text
Camera
  |
Capture / Pre-processing
  |
Hand Tracking (21 landmarks x up to 2 hands)
  |
Tracking Adapter + Calibration + Smoothing
  |
Interaction Engine ----------------------> Debug / Telemetry
  |
Topology / Pattern State Machine <------> AI Opponent / Turn Engine
  |
String Geometry Builder
  |
TouchDesigner Renderer + FX + UI
  |
Display / Player Feedback
```

The topology/game model is the source of truth. TouchDesigner is primarily responsible for camera/rendering/orchestration and must not become the canonical game-state store.

## Delivery milestones

### M1 - Tracking foundation
Camera -> two-hand tracking -> calibration -> smoothing -> selected finger anchors -> virtual string -> debug overlay -> record/replay fixture.

### M2 - First interaction
Implement one reliable, state-aware hook/grab interaction with hover, engage, attach, pull, commit, and release phases.

### M3 - First game transition
Human move -> validation -> topology change -> scripted AI move -> next player state.

### M4 - First complete pattern
Complete one supported Cat's Cradle sequence with UX feedback, error recovery, regression tests, UAT, and PVT.

## Documentation

- `docs/architecture.md` - components, canonical model, interaction pipeline, risks
- `docs/test-plan.md` - test strategy and core test cases
- `docs/uat-plan.md` - user acceptance testing and sign-off
- `docs/pvt-plan.md` - production verification and rollback
- `AGENTS.md` - implementation contract and working rules for Codex

## Development principle

Do not begin with full rope physics. The MVP uses a deterministic topology/state-machine model. Physics can later enhance rendering without owning game semantics.
