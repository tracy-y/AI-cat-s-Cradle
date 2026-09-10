# Test Plan

## Strategy

Use a test pyramid. Pure domain/topology logic receives the most automation. Camera and TouchDesigner behavior uses deterministic landmark replay plus focused live/manual visual verification.

| Level | Focus | When |
|---|---|---|
| Unit | Math, smoothing, hit detection, topology mutation, FSM | Every PR |
| Schema/config | Invalid IDs/crossings/pattern transitions/config | Every PR |
| Integration | Landmark replay -> events -> topology -> state | Every PR |
| TD integration | Python/TD exchange, geometry, reset/reload | CI where feasible + manual |
| System | Live end-to-end, invalid actions, tracking loss, performance | Release candidate |
| UAT | Usability and expected gameplay | Before release |
| PVT | Critical deployed-environment smoke | After deployment |

## Core test cases

| ID | Scenario | Action | Expected | Priority |
|---|---|---|---|---|
| TC-001 | Camera starts | Launch with supported camera | Preview visible, FPS reported, no fatal error | P0 |
| TC-002 | Two-hand detection | Show both hands | Two stable hand IDs + landmarks | P0 |
| TC-003 | Temporary hand loss | Hide/re-show one hand | Safe pause/ignore; reacquire without crash | P0 |
| TC-004 | Coordinate mapping | Move fingertip to known calibration points | Anchor aligns within agreed tolerance | P0 |
| TC-005 | Jitter filtering | Replay noisy fixture | Smoother output without unacceptable lag | P1 |
| TC-006 | String follows anchors | Move mapped fingers | Continuous connected geometry updates | P0 |
| TC-007 | Eligible hover | Approach target segment | One hover event/state | P1 |
| TC-008 | False hover rejection | Remain outside threshold | No interaction event | P1 |
| TC-009 | Valid hook/grab | Perform expected hook motion | ENGAGE then ATTACH; correct segment | P0 |
| TC-010 | Invalid segment | Hook non-target segment | Rejected; topology unchanged | P0 |
| TC-011 | Pull | Move attached finger | Geometry follows; constraints remain valid | P0 |
| TC-012 | Commit | Cross valid threshold | State changes exactly once; history records it | P0 |
| TC-013 | Debounce | Oscillate around threshold | No duplicate transitions/event storm | P0 |
| TC-014 | AI turn | Commit player move | Correct AI transition; returns PLAYER_TURN | P0 |
| TC-015 | Full pattern | Execute all prescribed moves | Expected final state, no corruption | P0 |
| TC-016 | Reset | Reset from mid-game | Initial canonical state + visuals restored | P0 |
| TC-017 | Invalid pattern config | Load malformed fixture | Clear validation failure; no partial start | P1 |
| TC-018 | Low confidence | Attempt action with poor tracking | Interaction suppressed + tracking indication | P1 |
| TC-019 | Performance | Run live for 10 minutes | Meets agreed FPS/latency; no runaway errors/memory | P0 |
| TC-020 | Replay determinism | Replay identical fixture repeatedly | Same events and states every run | P0 |
| TC-021 | Crossing semantics | Transition known over/under crossing | Correct over-segment retained/updated | P0 |
| TC-022 | Invalid-move recovery | Invalid action then valid action | Game remains playable; valid action succeeds | P0 |

## Quality gates

- CI must be green on release commit.
- All P0 automated/integration/system cases must pass.
- No open Severity 1/2 defects at UAT sign-off.
- Visual changes require reproducible manual verification evidence when they cannot be automatically asserted.
- Release candidate must be identified by exact commit/tag in test evidence.
