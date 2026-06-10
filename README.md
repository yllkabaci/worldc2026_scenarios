# Verification Track — Holdout Scenarios

> **DO NOT GIVE ANY FILE IN THIS FOLDER TO THE BUILD AGENT.**
> These are the **hidden** behavioral scenarios (the Scenarios Track of the Dark Factory pipeline). They are run during the Verification Audit, *after* the build, against the running API over HTTP only.

## Rules of use
1. The build agent never sees these files during a build. They live **outside** `specs/` for that reason.
2. Run every scenario against the built system **through its HTTP API** — never by reading the source.
3. On a failure: identify the violated behavior, **refine the specification** (`SPEC.md` / `specs/**`), and re-run the build. **Do not hand-edit implementation files.**
4. These verify **Quality Gate 2** (`SPEC.md §8`). They complement the exhaustive scoring-engine unit tests and the review agents (Gates 1 & 3).

## Contents
- `holdout-scenarios/00-reference-data.md` — shared datasets (A and B) with verified expected numbers.
- `holdout-scenarios/01-auth.md` … `05-leaderboard.md` — per-feature scenarios for the spine.

## Conventions
- Scenarios are written from the outside: **setup → action → expected observable outcome**.
- Time is controlled via the injectable clock (`IClock`); the football provider is the deterministic twin.
- Scoring uses the MVP base values (exact `3` / winner `1` / draw `1` / miss `0`); knockouts score on the regulation 90-minute result.
- The two invariants worth demoing live: **order-independence** and **coin/points conservation** (see `04-scoring-settlement.md`).
