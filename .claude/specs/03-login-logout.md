# Spec: Login and Logout

## Overview
Implement the logout flow and make the navbar session-aware so authenticated users see a "Sign out" link instead of "Sign in" / "Get started". The `login` route's POST handler was already implemented in Step 2; this step completes the authentication lifecycle by wiring up `GET /logout` (clear session, redirect) and updating `base.html` to reflect the current session state in the navigation bar.

## Depends on
- Step 1 (Database Setup) — `users` table and `get_db()` must exist.
- Step 2 (Registration) — `session["user_id"]` and `session["user_name"]` set on login must exist.

## Routes
- `GET /logout` — clear the Flask session, flash a goodbye message, redirect to `/` — public (but only meaningful when logged in)

## Database changes
No database changes.

## Templates
- **Modify:** `templates/base.html` — make the navbar session-aware:
  - When `session.user_id` is **not** set: show "Sign in" and "Get started" links (current behaviour)
  - When `session.user_id` **is** set: show the user's name and a "Sign out" link; hide "Sign in" / "Get started"

## Files to change
- `app.py` — implement the `logout` view function: call `session.clear()`, flash "You have been signed out.", redirect to `url_for("landing")`
- `templates/base.html` — wrap nav links in a `{% if session.user_id %}` / `{% else %}` block

## Files to create
None.

## New dependencies
No new dependencies.

## Rules for implementation
- No SQLAlchemy or ORMs — raw `sqlite3` only
- Parameterised queries only — never f-strings in SQL
- Passwords hashed with `werkzeug`
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Use `session.clear()` (not manual `session.pop`) to wipe all session keys on logout
- After logout: redirect to `url_for("landing")` with a flash message "You have been signed out."
- The navbar logged-in state must display `session["user_name"]` so the user knows whose account is active
- Do not redirect already-logged-in users away from `/login` or `/register` in this step — that is deferred to a later step

## Definition of done
- [ ] `GET /logout` clears the session and redirects to `/`
- [ ] After logout, the flash message "You have been signed out." is visible on the landing page
- [ ] After logout, the navbar shows "Sign in" and "Get started" again (not the user's name)
- [ ] When logged in, the navbar shows the user's name and a "Sign out" link instead of "Sign in" / "Get started"
- [ ] Clicking "Sign out" in the navbar calls `GET /logout` and lands back on the landing page as a guest
- [ ] The "Sign out" link uses `url_for("logout")` — no hardcoded URLs
- [ ] Logging in again after logout works correctly (session is clean, no stale data)
