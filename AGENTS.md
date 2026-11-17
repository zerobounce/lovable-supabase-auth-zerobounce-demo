# AGENTS.md

Guidance for AI coding agents working in this repository.

## Project

A demo that adds real-time ZeroBounce email validation to Supabase Auth sign-up in a Lovable app. It is two files that a user hands to Lovable's AI assistant, plus a README of production controls that points at the blog walkthrough. It is not a package and has no build.

## Layout

- `zb_migration.sql`: the Supabase migration. It creates `public.app_secrets` (where the API key lives), `public.zb_email_validation` (the validation log), `validate_email_with_zerobounce`, and a `BEFORE INSERT` trigger on `auth.users` that blocks a sign-up ZeroBounce rejects.
- `example_auth_form.tsx`: the sign-up form, including the "Did you mean…" suggestion.
- `README.md`: the production controls, and the link to the blog walkthrough.

## Commands

There are none to run locally. CI (`.github/workflows/ci.yml`) checks that the migration still defines the validation function, the signup block, and the false-success guard, and it runs those guards in Postgres with no HTTP call. Sign-up still fails open on a transport error unless `ZEROBOUNCE_FAIL_CLOSED` is set.

## Conventions

- **The API key never reaches the client.** It is read inside the database from `public.app_secrets`. Do not move it to the frontend, an environment variable the browser can read, or the migration file.
- **Keep the security controls.** The migration revokes `EXECUTE` on the validation function and all table access from `anon` and `authenticated`, and enables row-level security with deny-all policies. A change must not loosen any of them.
- **Fail closed when there is no key.** Without a key, addresses are not treated as valid.
- **This demo and the Bolt.new demo share the same `zb_migration.sql`.** Change both together.

## Changelog

There is no `CHANGELOG.md`. This repository is not a published package, so there is no version history to keep.

## Releasing

There is no registry and no version to tag. Readers use the files on `main`. Do not tag or publish unless a maintainer asks.
