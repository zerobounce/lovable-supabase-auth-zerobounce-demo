# 💌 ZeroBounce + Supabase Auth Integration (Lovable Cloud Guide)
This repository is part of the **ZeroBounce Blog Post + Video Tutorial**, showing how to add **real-time email validation** to your **Lovable app** using **ZeroBounce** and **Supabase Auth**.

You’ll learn how to build a **production-ready signup flow** that catches typos, blocks disposable emails, and gives helpful “Did you mean…” suggestions — all powered by the ZeroBounce API.

Remix with <a href="https://lovable.dev/projects/53eeac2c-f1a9-4a55-a2b5-b1046abaf21e?magic_link=mc_3adc5f4c-95e3-425c-8fcf-1229d21044a2"><img src="https://lovable.dev/img/logo/lovable-logo-icon.svg" width="10" alt="View in Lovable" /> Lovable</a>

📖 **Read the full blog post:** [Smarter Supabase Signup Flow with ZeroBounce Email Validation](https://www.zerobounce.net/blog/email-resources/email-verification/smarter-supabase-signup-flow)

## Required production controls

The migration keeps the original fail-open behavior for ZeroBounce transport failures,
non-2xx responses, and unrecognized statuses. To deny signup in those cases, add a row to
`public.app_secrets` with `name = ZEROBOUNCE_FAIL_CLOSED` and `value = true`. Set it to
`false` (or remove the row) to restore the original behavior. Known invalid statuses and
missing API keys still block signup regardless of this flag. Rejection events are written
to PostgreSQL logs without raw email addresses; configure log retention and access.

For CAPTCHA, enable protection with hCaptcha in **Supabase Dashboard → Authentication →
Bot and Abuse Protection** and enter the hCaptcha secret key there. This Supabase Auth
setting is the server-side enforcement switch. In the app consuming
`example_auth_form.tsx`, set `VITE_ENABLE_AUTH_CAPTCHA=true` and set
`VITE_HCAPTCHA_SITE_KEY` to the public site key. The frontend flag only renders the widget
and sends its token; it cannot enforce CAPTCHA by itself. The example sends tokens for
both sign-up and sign-in because the Supabase setting protects Auth flows. To turn it off,
disable protection in Supabase first, then set the frontend flag to `false`.

Configure Supabase Auth signup rate limits separately. Every signup attempt that reaches
the database trigger can spend a ZeroBounce API credit, whether CAPTCHA is enabled or not.
Verify that repeated automated attempts are stopped before they reach the trigger.
