# Holdout Scenarios — 03 Predictions

Covers `specs/features/03-predictions.md`. Distinct verbs: `POST` create, `PUT` modify. Goals 0–20. Deadline = kickoff − 60m.

## Happy path
### P1 — Create a prediction
**Setup:** M1 OPEN; Alice authenticated.
**Action:** `POST` prediction 2–1 for M1.
**Expected:** stored as Alice's single M1 prediction, timestamped; `201`.

### P2 — Modify before deadline (last write wins)
**Setup:** Alice already predicted M1 = 0–0; M1 still OPEN.
**Action:** `PUT` M1 prediction = 2–1.
**Expected:** the stored prediction becomes 2–1; on settlement only 2–1 counts (→ exact, 3 points). Earlier values have no effect.

### P3 — List active predictions
**Action:** `GET /api/predictions/me`.
**Expected:** returns Alice's predictions awaiting results.

## Error
### P4 — Duplicate create → 409
**Setup:** Alice already has an M1 prediction; M1 OPEN.
**Action:** `POST` a second M1 prediction.
**Expected:** `409` (one active prediction per match, BR-005). The way to change it is `PUT` (P2).

### P5 — After the deadline → 409
**Setup:** clock set to M1 kickoff − 59m (1 minute past lock).
**Action:** Alice `POST`s or `PUT`s an M1 prediction.
**Expected:** `409` (`PredictionWindowClosed`); the stored prediction (if any) is unchanged — even if the match is later delayed (BR-007).

### P6 — Out-of-range goals → 400
**Action:** submit predictions `-1`/`2`, then `21`/`0`, then `1.5`/`2`.
**Expected:** each `400`; nothing stored. A valid `3`/`2` afterward succeeds.

### P7 — Blocked user → 403
**Setup:** Carol's account is blocked.
**Action:** Carol submits a prediction.
**Expected:** `403`; nothing stored (BR-009).

## Edge
### P8 — Predicting a not-yet-determined knockout match
**Setup:** knockout match M9 exists with teams still undetermined (PENDING).
**Action:** Bob predicts M9.
**Expected:** rejected. After the provider sets M9's two teams and while it is before the deadline, the same `POST` succeeds.

### P9 — Predictions hidden before lock
**Setup:** M1 OPEN; Alice predicted 2–1.
**Action:** Bob requests Alice's M1 prediction (or the match's prediction list) while M1 is OPEN.
**Expected:** Alice's score is **not** disclosed. After M1 locks, it may be revealed.
