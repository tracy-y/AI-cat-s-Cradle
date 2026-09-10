# Technical Architecture

## 1. System pipeline

```text
Camera
 -> Capture / preprocessing
 -> Hand tracking
 -> Tracking adapter
 -> Calibration + smoothing + confidence gate
 -> Interaction engine
 -> Pattern FSM / topology engine
 -> Virtual opponent / turn orchestration
 -> Geometry builder
 -> TouchDesigner renderer
 -> Player feedback
```

Telemetry observes tracking, interaction, state transitions, rendering health, FPS, and errors throughout the pipeline.

## 2. Components

| Component | Responsibility | Suggested implementation |
|---|---|---|
| Capture | Camera selection, frames, mirror/crop, FPS | TouchDesigner Video Device In TOP or Python/OpenCV |
| Hand Tracking | Handedness, 21 landmarks, confidence | MediaPipe Hand Landmarker or equivalent |
| Tracking Adapter | Normalize coordinates, stable hand IDs, loss/reacquire | Python |
| Calibration | Camera/render coordinate mapping and tolerances | Python/config |
| Interaction Engine | Proximity, crossing, hook/grab, pull, release, debounce | Pure/testable Python where possible |
| Topology Engine | Anchors, segments, crossings, attachments, over/under | Python domain model |
| Pattern FSM | Legal states, expected actions, transitions | Python + validated JSON/YAML |
| Opponent Engine | Scripted opposing transitions and animation targets | Python |
| Geometry Builder | Convert canonical topology into renderable curves | Python + SOP/CHOP/DAT adapter |
| Renderer | String, glow, hints, feedback, particles/UI | TouchDesigner |
| Telemetry | FPS, confidence, state, event stream, failures | Structured logs + TD debug overlay |

## 3. Canonical game state

Conceptual model:

```text
GameState
- session_id
- phase: CALIBRATION | PLAYER_TURN | VALIDATING | AI_TURN | COMPLETE | ERROR
- pattern_id
- state_id
- hands[]
- anchors[]
    id
    owner
    landmark
    position
    attached_segments[]
- segments[]
    id
    from_anchor
    to_anchor
    control_points
    tension
- crossings[]
    segment_a
    segment_b
    over_segment
- active_gesture
- expected_action
- transition_history[]
- timestamp
```

The game state is canonical. Render geometry is derived data.

## 4. Interaction state model

1. **TRACK** - maintain stable hand/finger coordinates and confidence.
2. **HOVER** - active finger enters proximity of an eligible segment.
3. **ENGAGE** - trajectory/gesture satisfies the current expected action.
4. **ATTACH** - logical attachment is created/changed and visual confirmation is emitted.
5. **PULL** - segment follows the attached finger while constraints remain valid.
6. **COMMIT** - transition threshold is crossed and FSM validates the move.
7. **RELEASE** - update attachment/crossing state and settle geometry.
8. **AI TURN** - opponent animates the prescribed transition, then returns control.

Recognition must be state-aware. A geometric intersection by itself is not a valid move.

## 5. Suggested repository structure

```text
AI-cat-s-Cradle/
├── README.md
├── AGENTS.md
├── docs/
│   ├── architecture.md
│   ├── interaction-spec.md
│   ├── topology-model.md
│   ├── pattern-authoring.md
│   ├── test-plan.md
│   ├── uat-plan.md
│   ├── pvt-plan.md
│   └── adr/
├── touchdesigner/
│   ├── virtual_cats_cradle.toe
│   ├── components/
│   └── assets/
├── src/
│   ├── tracking/
│   ├── calibration/
│   ├── interaction/
│   ├── topology/
│   ├── patterns/
│   ├── opponent/
│   ├── geometry/
│   ├── telemetry/
│   └── common/
├── config/
│   ├── default.json
│   └── patterns/
├── schemas/
│   ├── landmark-frame.schema.json
│   └── game-state.schema.json
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── fixtures/
│   ├── recorded_sessions/
│   └── acceptance/
├── scripts/
│   ├── run_tracker.py
│   ├── replay_session.py
│   └── validate_patterns.py
└── .github/workflows/
```

## 6. Non-functional requirements

- Target >=30 FPS on development hardware; measure baseline and regressions.
- Instrument capture-to-render latency before setting a hard threshold.
- No crash/topology corruption on temporary hand loss, invalid moves, or reset.
- Same initial state + same event sequence = same final topology.
- Debug overlay exposes FPS, confidence, phase/state, target segment, active gesture, and last event.
- Thresholds/smoothing/camera options/patterns are external configuration.
- Camera processing is local by default; raw video persistence is opt-in.

## 7. Primary risks

| Risk | Mitigation |
|---|---|
| Finger/line occlusion | Confidence gating, temporal history, expected-action context |
| 2D under/over ambiguity | z estimate + trajectory + expected action; consider depth/second camera later |
| Monolithic TD project | Keep domain model and logic in Python modules |
| False grabs from jitter | Smoothing, hysteresis, dwell/debounce thresholds |
| Rope physics instability | Deterministic topology first; physics only as visual enhancement |
| Live CV difficult to test | Record/replay normalized landmark streams |
| Pattern authoring too code-heavy | Validated data-driven pattern definitions |

## 8. Delivery stages and exit gates

1. **Discovery** - chosen MVP pattern and measurable setup documented.
2. **Skeleton** - CI green, application shell starts, camera preview visible.
3. **Tracking** - stable two-hand tracking and replayable fixture.
4. **Virtual String** - calibrated anchors drive a continuous rendered loop.
5. **Interaction** - recorded fixtures generate deterministic gesture events.
6. **Topology/FSM** - chosen pattern transitions validate from data.
7. **Opponent** - player -> AI -> player loop succeeds repeatedly.
8. **UX/FX** - game is usable without developer console.
9. **System Test** - critical defects closed and regression report passes.
10. **UAT** - acceptance sign-off against exact release candidate.
11. **Release/PVT** - target-machine smoke tests pass or release is rolled back.
