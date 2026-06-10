# Holdout Scenarios — 01 Authentication

Covers `specs/features/01-auth.md`. MVP auth: email/password, active on registration, JWT on login, no verification/lockout.

## Happy path
### A1 — Register then login
**Setup:** empty system.
**Action:** register `alice@example.com` with a BR-017-compliant password; then log in.
**Expected:** registration succeeds and creates an **active** `User` with role `User`; login returns a **JWT** whose claims include `sub`, `email`, and `role=User`.

### A2 — Token grants access to a protected endpoint
**Action:** call a `User`-protected endpoint (e.g. `GET /api/predictions/me`) with the issued token.
**Expected:** `200` (not `401`).

## Error
### A3 — Duplicate email
**Setup:** `alice@example.com` already registered.
**Action:** register `alice@example.com` again.
**Expected:** `409`; no second account created.

### A4 — Wrong password
**Action:** log in as `alice@example.com` with an incorrect password.
**Expected:** `401`; response does not reveal which field was wrong; no token issued.

### A5 — Missing token
**Action:** call any protected endpoint with no `Authorization` header.
**Expected:** `401`; no state change.

### A6 — Weak password rejected
**Action:** register with `password` = `"abc"` (fails BR-017).
**Expected:** `400` ProblemDetails with a validation error on the password field; no account created.

## Edge / authorization
### A7 — New users are not admins
**Setup:** Alice registered (role `User`).
**Action:** call an `Admin`-only endpoint (e.g. set official result) with Alice's token.
**Expected:** `403`. Self-registration never grants elevated roles.
