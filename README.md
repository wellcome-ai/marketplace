# Wellcome Marketplace

A Claude Code marketplace for accelerated build days — guide non-technical builders through shipping a working app in a single day.

## What's in here

One plugin: **`wellcome`**, providing two commands:

- **`/wellcome:create-app`** — asks eight short questions, then silently sets up the project and iteratively builds the app to a working v1.
- **`/wellcome:publish-app`** — puts a finished app online under the builder's own free Vercel account and hands back a shareable link.

Every technical decision (framework, package manager, database, file structure, deploy target) is pre-made so the builder can focus on *what* they want, not *how* to set it up.

## Installation

> **Heads-up for the Claude desktop app:** as of mid-2026 the in-app `/plugin` slash command is **not available** in the desktop app — typing it returns *"/plugin isn't available in this environment."* Install through the **Customize** menu instead (steps just below). The `/plugin …` slash commands further down only work in the **terminal** Claude Code interactive session.

**In the Claude desktop app (recommended for participants):**

1. Open **Customize**.
2. Under **Personal Plugins**, choose the **+**.
3. Select **+ Create Plugin** → **Add marketplace** → **Add from a repository**.
4. Enter the repository URL: `https://github.com/wellcome-ai/marketplace`
5. Once the marketplace is added, install the **wellcome** plugin from it.

**In the terminal Claude Code session:**

```bash
# From GitHub:
/plugin marketplace add wellcome-ai/marketplace

# Or locally for development:
/plugin marketplace add /path/to/marketplace

# Then install the plugin:
/plugin install wellcome@wellcome-ai
```

**Prepping machines non-interactively (e.g. build-day setup)** — run the same thing via the `claude` CLI binary in a shell, no interactive session needed:

```bash
claude plugin marketplace add wellcome-ai/marketplace
claude plugin install wellcome@wellcome-ai
```

The repo is public, so no GitHub authentication is required for the install.

After installing, **restart the Claude desktop app**, then run in an empty directory:

```bash
/wellcome:create-app
```

When the app is built and you want it online, run `/wellcome:publish-app` from inside the app folder — it deploys to your own free Vercel account (one browser sign-in) and gives you a public link to share.

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

## Running a build day?

If you're the facilitator setting up a room of machines, see [`docs/build-day-setup.md`](docs/build-day-setup.md) for the per-machine prep checklist and what to tell participants on the day.
