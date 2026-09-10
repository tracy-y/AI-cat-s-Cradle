# Production Verification Testing (PVT)

PVT runs against the exact released build on the intended machine/camera immediately after deployment. It is intentionally short and critical-path focused.

## PVT smoke suite

| ID | Verification | Expected |
|---|---|---|
| PVT-01 | Launch released build | Starts with expected version/config |
| PVT-02 | Camera + hand tracking | Camera opens; two hands track; health/confidence normal |
| PVT-03 | Calibration | Mapping, orientation and mirroring are correct |
| PVT-04 | Initial string | Expected segments/crossings render correctly |
| PVT-05 | One player transition | Valid interaction updates topology exactly once |
| PVT-06 | One opponent transition | Opponent completes transition and returns control |
| PVT-07 | Reset/recovery | Reset restores initial state |
| PVT-08 | Telemetry | No critical errors; version/session metadata and logs present |

## PVT decision

**PASS:** all P0 smoke checks pass. Release is accepted.

**FAIL:** if any P0 smoke check fails:
1. Stop verification and preserve logs/evidence.
2. Record failing build/tag, hardware, camera, configuration, and reproduction steps.
3. Revert to the last known-good release tag/config where applicable.
4. Raise a defect linked to the failed PVT case.
5. Fix and run automated/system regression before redeploying.
6. Execute the full PVT smoke suite again after redeployment.

## Release sign-off checklist

- CI green on release commit.
- All P0 test cases pass.
- Supported pattern completes repeatedly from clean start.
- UAT sign-off recorded against exact release candidate.
- No Severity 1/2 defects open.
- Version, config, runbook, known issues and rollback instructions frozen.
- PVT evidence captured on target setup.
- Production/release tag accepted only after successful verification.
