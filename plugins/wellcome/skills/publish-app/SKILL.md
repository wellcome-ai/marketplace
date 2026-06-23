---
description: Put a finished build-day app on the internet with a shareable link. For non-technical users who have built an app with /wellcome:create-app and want it live on a public URL. Deploys the current Next.js app to the user's own Vercel account via the Vercel CLI, after confirming the build is healthy.
disable-model-invocation: true
---

# Publish App

You are helping a **non-technical user** put the app they built today onto the internet, so they can share a link with anyone. They built it with `/wellcome:create-app` and now want it live. Your job is to deploy the app sitting in the current directory to **their own Vercel account** and hand them a working public URL — with as little friction and jargon as possible.

## Operating mode — read this first

Same stance as the build skill: **make the technical decisions yourself, don't ask the user.** They cannot evaluate them.

- Run the checks, the login, and the deploy yourself. Don't narrate each command.
- If a step fails, read the error, try a fix, retry. Only surface a failure when you've exhausted reasonable options — and then in plain English, not a stack trace.
- In the normal flow the user only has to do **one** thing themselves: approve the Vercel login in their browser (you cannot click that for them). Everything else is yours.
- Never ask "should I…?" — just do it. Never offer choices on project name, region, framework settings, or deploy flags.
- Be encouraging and brief. Avoid jargon. No walls of text.
- Never mention git, commits, or branches unless they ask. Git is plumbing — keep it invisible.

## What "publish" means here

The app runs in **local mode** — its data lives in each visitor's own browser (localStorage), with no shared database yet. Publishing does **not** change that. So be honest in the final report: the live site works and is shareable, but every visitor gets their own private copy of the data, and nothing is saved across devices, until they connect Supabase (the generated README has the upgrade path). Don't imply the deployed app has a shared backend when it doesn't.

Deploying is the right move anyway — it proves the app is real, gives them a link to show people, and the moment they add real keys the same URL upgrades in place.

---

## The flow

Four steps, in order. Do them silently except where noted.

1. **Pre-flight** — confirm we're in a real built app and that it builds cleanly. Never deploy a broken build.
2. **Connect Vercel** — the one step the user does themselves: `npx vercel login` to their own account.
3. **Deploy** — `npx vercel --prod --yes`.
4. **Report** — hand them the live link, plainly, with the honest caveat.

---

## Step 1 — Pre-flight (silent)

Before anything else, confirm two things. If either fails, do **not** deploy.

### 1a. Confirm we're in a generated app

Check the current directory is a Next.js app built by the build skill:

- A `package.json` exists and lists `next` in its dependencies.
- A `next.config.ts` (or `next.config.js`) exists.

If the current directory is **not** an app (e.g. it's the empty folder they ran the build from, with the app in a subdirectory), look for a single obvious app subdirectory and `cd` into it — and say which one in one short line ("Publishing **{folder}**…"), so a wrong guess is caught before you deploy. If there are several and it's ambiguous which one they mean, ask one plain question: "Which app do you want to publish?" and list the folder names. This is the rare case where you must ask — you need a value only they have.

### 1b. Confirm the build is healthy

First make sure dependencies are installed, then run a production build:

```bash
npm install
npm run build
```

`npm install` is idempotent — quick if everything's already there, essential if it isn't. A fresh session or a reset machine won't have `node_modules` (it's never committed), so run it first; otherwise a missing-dependency error masquerades as a broken build.

- If `npm run build` exits 0 → continue.
- If it fails → read the error and fix it the same way the build skill does. **If the error is about missing modules or `next: command not found`, the fix is simply `npm install` — not a code change**, so don't thrash trying to "fix" the app. A broken build deployed is worse than no deploy, so do not move past this step until `npm run build` is green. If you genuinely cannot fix it after reasonable attempts, stop and tell the user in plain English what's wrong and that you didn't publish, rather than deploying something broken.

Don't narrate this step beyond a brief "Checking your app builds cleanly…".

---

## Step 2 — Connect Vercel (the one interactive step)

The app deploys to the **user's own** Vercel account, so they have to sign in once. First check whether they're already signed in (the first `npx vercel …` you run may download the Vercel CLI on the way — that's expected, let it finish):

```bash
npx vercel whoami
```

- If it prints an account name → they're already connected. Skip to Step 3.
- If it errors / prints nothing → they need to log in.

Logging in uses Vercel's **device flow**. When you run `npx vercel login`, the CLI prints a line like:

```
Visit https://vercel.com/oauth/device?user_code=XXXX-XXXX
Waiting for authentication...
```

and then **blocks, waiting** for the user to approve in a browser. It does **not** pop open a browser by itself in this context, and there's no terminal menu to drive — so your job is to hand that exact link to the user. Because the command blocks, **run it so you can read its output while it keeps waiting** (start it in the background and watch its output, rather than a plain foreground call that would just hang). Read the `Visit …` URL it printed, then give it to the user verbatim:

> To put your app online, open this link and sign in to Vercel — it's free: **{the exact `https://vercel.com/oauth/device?user_code=…` URL}**. "Continue with Google" or "Continue with GitHub" is quickest. Approve the code it shows you, then come back — I'll take it from there.

In the normal flow this sign-in is the only thing they do themselves — you can't approve it for them. Once they approve, the `vercel login` command finishes on its own; then confirm it worked with `npx vercel whoami` before moving on. If it times out or they close the tab, just run the login again.

---

## Step 3 — Deploy

From inside the app directory, deploy straight to production:

```bash
npx vercel --prod --yes
```

`--yes` accepts every setup prompt with sensible defaults: on a first deploy it creates a new Vercel project named after the folder and links it; on a later deploy it reuses that project. The build runs on Vercel's servers (that's why Step 1b matters — you want it already known-good).

Give one brief progress line ("Putting your app online…") and let it run. It takes under a minute.

### Read the output for the right URL

The command prints two URLs:

```
Production: https://your-app-a1b2c3xyz-yourname.vercel.app   ← long, per-deploy; may sit behind a Vercel login
Aliased:    https://your-app-lac.vercel.app                  ← clean, stable, public — give THIS to the user
```

Use the **`Aliased:`** URL — it's the clean, stable, public address and the one to share. **Read it verbatim off the `Aliased:` line and use that exact string; never rebuild it from the app's name.** Vercel sanitises the folder into a project slug and appends a short unique suffix (e.g. `-lac`, `-one-bice`), so the real alias is `https://<project-slug>-<suffix>.vercel.app`, not a bare `https://<app-name>.vercel.app` — reconstructing it from the app name gives a wrong host. Only fall back to the `Production:` URL if no `Aliased:` line is printed at all.

### Verify it's actually live

Before you tell the user it's live, confirm the link really serves their app. Curl the **exact** Aliased URL you just read — copy it verbatim, don't reconstruct it:

```bash
curl -sS -o /dev/null -L -w "%{http_code} %{url_effective}" "<the exact Aliased URL from the output>"
```

`-L` follows redirects, so a routine `307`/`308` (e.g. path normalisation) resolves to the real final status instead of looking like a failure; `%{url_effective}` shows where it ended up. Read **both** the status and the final URL:

- **`200`, and the final URL is still on the app's own `*.vercel.app` domain** → it's live. Report it.
- **The final URL is a Vercel login / SSO page** (it lands on `vercel.com/login` or a Vercel "Authentication Required" page — whatever the status code) → the deploy worked, but Vercel's **Deployment Protection** (a.k.a. **Vercel Authentication**) is gating the link, so visitors are asked to log in. Tell it apart by *where it lands*: a **Vercel** page means protection; the app's own pages (even an error one) do not. This is uncommon on a fresh free account, and it's an account-level setting you can't flip from here on the user's behalf. So be honest: tell them the app deployed fine but Vercel is currently keeping it private, and point them to turn off **Deployment Protection / Vercel Authentication** for this project in their Vercel dashboard's project **Settings**, then run `/wellcome:publish-app` again to re-check. Don't report a login-walled link as "shareable" — it isn't until protection is off.
- **Any other result — a non-200 that stayed on the app's own domain (a 500, a 404, or a connection error right after deploying)** → don't assume protection. A just-finished deploy can take a few seconds to start serving, so wait a moment and curl the same URL once more. If it's still failing, it's the app, not a Vercel setting — read what the page or `npx vercel logs <url>` says and fix it the way the build skill would, then re-deploy. Don't loop on this: after a reasonable attempt with no obvious fix, tell the user plainly that the app deployed but its page isn't loading yet, rather than redeploying over and over.

If the deploy itself errors, read the message, fix what you can (a missing build output, a transient network error → retry), and only surface it if you're truly stuck.

---

## Step 4 — Final report

When the app is live and you've confirmed the link returns 200, tell the user the message below, substituting `{name}` and the real `{url}`. Keep it short and encouraging.

```
🎉 "{name}" is live!

Your link: {url}

Anyone you send it to can open it in their browser — no install, nothing to set up.

One thing to know: right now your app saves its data in each visitor's own browser, so people won't see each other's data and nothing is shared between devices yet. That's normal for today's build. When you're ready to give it a shared database (so everyone sees the same data), see the "Connect real services" section of your app's README — it walks through connecting Supabase, and your link stays the same.

To put a new version online later, just run /wellcome:publish-app again.
```

Don't list files. Don't mention git. Don't explain Vercel internals.

---

## A few hard rules

- **Never deploy a build that doesn't pass `npm run build`.** Step 1b is a gate, not a suggestion.
- **Deploy to the user's own account, via `npx vercel login` + `npx vercel --prod --yes`.** Don't deploy to a shared or facilitator account — the app should be theirs.
- **Give them the `Aliased:` (clean) URL, read verbatim from the output and verified at 200.** Never reconstruct the URL from the app name, and never hand over a link you haven't confirmed serves the app.
- **Be honest about local mode.** The live app stores data per-visitor in the browser until Supabase is connected. Say so; don't imply a shared backend exists.
- **Don't push secrets.** If the app has a real `.env.local` with keys, those are not uploaded automatically and you should not paste them anywhere public. Connecting real services (adding env vars in the Vercel dashboard) is a later, optional step covered by the README — mention it only if they ask.
- **Keep the user to the one interactive step — the browser login — in the normal flow.** The login is theirs; everything else is yours. The only exception is the rare account with Deployment Protection on (see Step 3's verify), where they flip one dashboard setting; name it plainly if it happens, but don't go looking for other things to hand them. Never make a non-technical user run or paste commands they don't understand.
