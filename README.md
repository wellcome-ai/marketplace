# Wellcome Marketplace

A Claude Code marketplace for accelerated build days — guide non-technical builders through shipping a working app in a single day.

## What's in here

One plugin: **`wellcome`**, providing the `/wellcome:create-app` command.

It asks the builder eight short questions, then silently sets up the project and iteratively builds their app to a working v1. Every technical decision (framework, package manager, database, file structure, deploy target) is pre-made so the builder can focus on *what* they want, not *how* to set it up.

## Installation

```bash
# From GitHub (once published):
/plugin marketplace add wellcome-ai/marketplace

# Or locally for development:
/plugin marketplace add /path/to/marketplace

# Then install the plugin:
/plugin install wellcome@wellcome-ai
```

To use it, run in an empty directory:

```bash
/wellcome:create-app
```

## What `/wellcome:create-app` asks

1. What do you want to call your app?
2. What does it do, and who's it for?
3. Will users sign in? *(toggles Supabase Auth)*
4. Will users upload files? *(toggles Supabase Storage)*
5. Which AI provider? *(Anthropic / OpenAI / Gemini / None)*
6. Will it send emails? *(toggles Resend)*
7. Will it have charts? *(toggles Recharts)*
8. Anything else important?

If your Q8 answer hints at needs like background jobs, payments, analytics, error monitoring, maps, or PDF generation, the right tool is added automatically — see "Auto-added when Q8 suggests it" below.

After confirmation, the plugin scaffolds the project in a new subdirectory and builds the app autonomously until a working v1 is in place.

## The stack

**Always included**

- Next.js (App Router) + TypeScript + React
- Tailwind CSS + shadcn/ui
- Supabase (Postgres)
- Vercel (recommended deploy target)

**Toggleable per build**

- Supabase Auth
- Supabase Storage
- AI SDK — one of Anthropic, OpenAI, or Google Gemini
- Resend + React Email
- Recharts (via shadcn/ui's chart components)

**Auto-added when Q8 suggests it**

- Trigger.dev — background jobs and scheduled tasks
- Stripe — payments and subscriptions
- PostHog — analytics
- Sentry — error monitoring
- Mapbox — maps and locations
- @react-pdf/renderer — PDF generation

## Who it's for

Workshop participants who:

- Have an app idea but don't write code day-to-day
- Are being coached or paired with someone more technical
- Have one day to ship something working

If you're a working developer, you probably want to pick your own stack — this plugin's whole point is to take that choice away.
