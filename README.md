# php-auth-test

A user registration and login module in plain PHP with Ajax forms, sessions and a "remember me" option.

## Context

This is a test assignment for a junior PHP developer position, written in February 2017. The README was updated in 2020 and 2024; the code itself has not changed since 2017. It is kept as an early-career snapshot of how the owner built authentication without a framework at the start of a PHP backend career. It does not reflect current security practice (see [Status](#status)).

## Features

- Registration with username, password and password confirmation; duplicate usernames are rejected.
- Login with username and password; the user is logged in automatically after registration.
- "Remember me": the session ID is stored in a cookie (`sid`, HttpOnly) for 7 days.
- Logout.
- All forms are submitted via Ajax to a single endpoint (`ajax.php`), which only accepts `POST` and returns JSON (`status`, `code`, `message`, `data`).
- Field-level error messages and a client-side redirect when the server returns one.
- Passwords are stored as salted, iterated hashes with a per-user salt.

## Tech stack

- PHP (no framework, no Composer), PDO
- MySQL
- jQuery 2.0.3
- Twitter Bootstrap 2.3.1

## Architecture

```
index.php                     login form, or a welcome page with logout when logged in
register.php                  registration form
ajax.php                      Ajax endpoint: login, logout, register actions
classes/AjaxRequest.class.php base class: dispatches the "act" parameter to a method, builds the JSON response
classes/Auth.class.php        Auth\User: DB connection, password hashing, login, registration, session handling
js/ajax-form.js               submits forms with class "ajax", shows errors, follows redirects
css/                          styles and reset
vendor/                       Bootstrap and jQuery
db_auth.sql                   users table schema
```

Key decisions:

- Request handling is separated from user logic: `AuthorizationAjaxRequest` (in `ajax.php`) extends a generic `AjaxRequest` wrapper and maps actions to methods, while `Auth\User` contains the database and session logic.
- All SQL uses PDO prepared statements; user creation runs in a transaction.

## Getting started

Requirements: PHP with the PDO MySQL extension and MySQL.

1. Create a database and import the schema:

   ```bash
   mysql -u <user> -p <database> < db_auth.sql
   ```

2. Set the database host, name, user and password in the private properties at the top of `classes/Auth.class.php` (`$db_host`, `$db_name`, `$db_user`, `$db_pass`).

3. Serve the project root (links point to `/` and `/register.php`), for example:

   ```bash
   php -S localhost:8000
   ```

   Then open http://localhost:8000.

## Testing

There are no automated tests.

## Status

Archived. Written in February 2017 and kept for history only; no further development is planned. Known limitations from that time: the password hash is built from MD5/SHA-1 rounds instead of `password_hash()`, database credentials are hard-coded in a class instead of configuration, and there is no CSRF protection.

## License

MIT, see [LICENSE](LICENSE).
