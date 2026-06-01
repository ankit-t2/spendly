# Spec: Registration

## Overview
Implement user registration so new visitors can create a Spendly account. This step upgrades the existing stub GET /register route into a fully functional form that accepts a POST, validates input, hashes the password, and inserts a new row into the users table. On success the user is shown with a success message and then redirected to the login page. This is the entry point for all authenticated features that follow.

## Depends on
- Step 1 (Database Setup) — `users` table, `get_db()`, `init_db()` must exist.

## Routes
- `POST /register` — process registration form — public
- `POST /login` — process login form, set session — public

## Database changes
No new tables or columns. The `users` table from Step 1 already has:
`id`, `name`, `email` (UNIQUE), `password_hash`, `created_at`.

New DB helper functions to add in `database/db.py`:
- `create_user(name, email, password)` — hashes password, inserts row, returns new `id`
- `get_user_by_email(email)` — returns a `Row` or `None`

## Templates
- **Modify:** `templates/register.html` — add `method="POST"` and `action="{{ url_for('register') }}"` to the form; add a block to display flash messages / inline errors
- **Modify:** `templates/login.html` — add `method="POST"` and `action="{{ url_for('login') }}"` to the form; add a block to display flash messages / inline errors
- **Modify:** `templates/base.html` — add a flash message banner so all pages can surface messages (used after redirect)

## Files to change
- `app.py` — add `SECRET_KEY`, import `session`/`redirect`/`url_for`/`flash`/`request`; convert `register` and `login` view functions to handle both GET and POST
- `database/db.py` — add `create_user()` and `get_user_by_email()` helper functions
- `templates/register.html` — wire up the form and error display
- `templates/login.html` — wire up the form and error display
- `templates/base.html` — add flash message banner

## Files to create
None.

## New dependencies
No new dependencies. `werkzeug.security` (`generate_password_hash`,
`check_password_hash`) is already available via Flask's dependency on Werkzeug.

## Rules for implementation
- No SQLAlchemy or ORMs — raw `sqlite3` only
- Parameterised queries only — never f-strings in SQL
- Passwords hashed with `werkzeug.security.generate_password_hash`; verified with `check_password_hash`
- `SECRET_KEY` must be set on the Flask `app` object before any session use; use a hard-coded dev string (e.g. `"dev-secret-change-in-production"`) — no env-var machinery needed at this stage
- Use `flask.session` to store the logged-in user; at minimum store `session["user_id"]` and `session["user_name"]`
- After successful registration: redirect to `/login` with a flash message "Account created — please log in."
- After successful login: redirect to `/` (landing) — placeholder until the dashboard exists in a later step
- On any error (duplicate email, wrong password, missing field): re-render the same form with an error message; do NOT redirect-on-error
- All templates must extend `base.html`
- Use CSS variables — never hardcode hex values
- Use `url_for()` for every internal link — never hardcode URLs

## Definition of done
- [ ] `POST /register` with valid data creates a new user row in the database and redirects to `/login`
- [ ] `POST /register` with a duplicate email re-renders `register.html` with a visible error (no new row created)
- [ ] `POST /register` with any empty field re-renders `register.html` with a visible error
- [ ] `POST /login` with correct credentials sets `session["user_id"]` and redirects to `/`
- [ ] `POST /login` with wrong password re-renders `login.html` with a visible error
- [ ] `POST /login` with an unknown email re-renders `login.html` with a visible error
- [ ] Flash messages from redirects appear in the browser (e.g. the "Account created" message on the login page)
- [ ] `app.py` has a `SECRET_KEY` set
- [ ] Passwords are never stored in plain text — only the hash appears in the database
