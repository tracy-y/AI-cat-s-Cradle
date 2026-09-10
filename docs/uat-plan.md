# User Acceptance Testing (UAT)

## Goal
Prove that a representative non-developer can calibrate the experience, understand the interaction cues, complete the supported Cat's Cradle sequence, and recover from ordinary mistakes.

## UAT scenarios

| ID | Scenario | Acceptance criterion |
|---|---|---|
| UAT-01 | Cold launch and hand calibration | User reaches READY/PLAYER_TURN without developer intervention |
| UAT-02 | Complete first supported move | Correct segment highlights/attaches and state advances |
| UAT-03 | Observe virtual opponent | Opponent move is understandable, correct, and clearly returns the turn |
| UAT-04 | Complete supported sequence | Final expected pattern reached without topology corruption |
| UAT-05 | Perform a wrong move | Move rejected without destroying valid state; useful feedback shown |
| UAT-06 | Hand leaves camera frame | State remains safe; user can continue or is clearly asked to recalibrate |
| UAT-07 | Restart mid-game | Known initial state is reliably restored |
| UAT-08 | Usability | User understands turn, target action, success, and failure without developer logs |

## Evidence to record

For every UAT execution capture:
- tester
- date/time
- exact commit/tag/build
- operating system
- TouchDesigner version
- camera model/setup
- scenario result (Pass/Fail/Blocked)
- screenshot/video/log evidence where useful
- defect/issue IDs
- comments

## Sign-off gate

UAT is approved only when:
- 100% of P0 acceptance scenarios pass;
- no Severity 1 or Severity 2 defects remain open;
- P1 usability issues have an agreed disposition;
- the tested release candidate commit/tag is recorded;
- any accepted limitations are documented as known issues.
