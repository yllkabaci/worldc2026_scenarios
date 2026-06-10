# Holdout Scenarios — Tier-2 (deferred features)

> Harvested from a teammate's `SCENARIOS.md`. These cover features **not in the MVP spine** (bonus predictions, stage multipliers, groups, account lockout/session, real-time). Keep them here in the external vault; activate each when its feature is built. Numbers (SC-XX) preserved from the source for cross-referencing.
>
> Spine scenarios already covered by files `01`–`05` are not duplicated here. Where a source scenario overlaps the spine, it is noted.

## Bonus predictions & multipliers (tier 2)
- **SC-02** — Exact score in a quarter-final (×1.5). With multipliers enabled: 3 × 1.5 = **4.5**. *Resolved:* points are `decimal`, **no rounding** → 4.5 stands (the source left rounding open).
- **SC-05** — Exact goal-difference bonus: predict 2-0, result 3-1 → winner (1) + goal-diff bonus (1) = **2**.
- **SC-14 / SC-15** — Minute of first goal within / outside ±5 → **+2** / **0**.
- **SC-16** — Minute of first goal, match ends 0-0 → **Void** (BR-026). *(Spine note: void-on-0-0 logic already asserted for scoring in `04`.)*
- **SC-17 / SC-18** — Penalty Yes when awarded in normal time → **+1**; Penalty No when only a shootout occurs → **+1** (shootout excluded, BR-027).
- **SC-19 / SC-20** — Substitutions: both teams exact → **+1**; one wrong → **0** (BR-028).
- Summary check: exact score + goal diff in QF → (3+1) × 1.5 = **6**.

## Groups (tier 2)
- **SC-24** — Group leaderboard uses **global** points, not a separate pool (BR-015).
- **SC-25** — Invite code expires after 30 days; joining with it is blocked (BR-012).
- **SC-26** — Group admin cannot remove themselves; must transfer role first (BR-014).
- **SC-27** — User already in 5 active groups cannot create/join a 6th (BR-016, configurable).

## Auth hardening (tier 2 — MVP auth is minimal)
- **SC-30** — 5 failed logins → account locked 15 min (BR-018).
- **SC-31** — 24h inactivity → session expired, redirect to login (BR-019).
- **SC-29** — Blocked user predicting via the API directly → **403** (BR-009). *(Spine note: blocked-cannot-predict is asserted in `03`; the direct-API angle is the tier-2 addition.)*
- **SC-28** — Blocking a user removes their standings from the leaderboard immediately (UC-A08). *(Tier 2 — spine leaves blocked-user removal deferred.)*

## Real-time (tier 2 — MVP is REST)
- **SC-32** — Admin updates a live score → connected clients see it within ~2s without refresh.
- **SC-33** — Admin confirms a result → affected totals and ranks update in real time.

> When a tier-2 feature is promoted into scope, move its scenarios into a numbered spine file (or keep here) and wire them into the verification run.
