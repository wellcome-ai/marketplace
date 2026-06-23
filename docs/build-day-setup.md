# Running a build day — facilitator setup

**For the course runner, not participants.** This is the prep checklist for whoever sets up the room before an accelerated build day. Participants don't install anything — they just open a folder and run `/wellcome:create-app` on the day. Your job is to make sure that command already works on every machine.

## Why pre-setup is required

As of mid-2026 the Claude **desktop app does not support the `/plugin` slash command** — typing it returns *"/plugin isn't available in this environment."* The plugin *can* be installed from inside the desktop app via the **Customize** menu (see option B below), but for a room of non-technical participants you don't want them clicking through setup and waiting on downloads during the session. Pre-install on each machine ahead of time — from a terminal (which writes to the shared `~/.claude/plugins` config the desktop app reads) or through the Customize menu — and the command is ready the moment they sit down. The marketplace repo is public, so no GitHub login is needed.

Do this ahead of time on each machine — not live during the session. About 5 minutes per machine.

## Per-machine checklist

### 1. Check prerequisites

```bash
node --version    # >= 20.9 required; 22 LTS or newer recommended (stack is validated on 24.x)
npm --version
git --version
claude --version  # the terminal Claude Code CLI must be installed
```

If Node is missing or below 20.9, install Node 22 LTS or newer (the stack is validated on 24.x; below 22 is untested). If `claude` is missing, install the Claude Code CLI first.

### Participant prerequisite — a free Vercel account (for publishing)

Building an app needs nothing external. **Publishing it does** — `/wellcome:publish-app` puts each participant's app online under *their own* Vercel account, so every participant who wants a live, shareable link needs a free Vercel account.

- It's free (Vercel's Hobby tier) and takes a minute to create. "Continue with Google" or "Continue with GitHub" is the fastest sign-up.
- They don't need it to build, only to publish — but publishing is the satisfying finish, so it's worth having ready.
- **Put this in the participant prep email** so people arrive with an account already created (or at least know they'll make one on the day). Ask them to sign up at <https://vercel.com/signup> beforehand. The publish step opens the browser login for them; an account that already exists makes that one click instead of a full sign-up mid-session.

Nothing to pre-install on the machines for this — the Vercel CLI runs on demand via `npx`. The only per-participant requirement is the account itself.

### 2. Install the plugin

Use whichever is faster for you. The terminal route scripts cleanly across many machines; the Customize route is all-GUI if you'd rather not touch a terminal.

**Option A — terminal**

```bash
claude plugin marketplace add wellcome-ai/marketplace
claude plugin install wellcome@wellcome-ai
```

Expect `✔ Successfully added marketplace: wellcome-ai` and `✔ Successfully installed plugin: wellcome@wellcome-ai`.

**Option B — Customize menu (in the desktop app)**

1. Open **Customize**.
2. Under **Personal Plugins**, choose the **+**.
3. Select **+ Create Plugin** → **Add marketplace** → **Add from a repository**.
4. Enter the repository URL: `https://github.com/wellcome-ai/marketplace`
5. Once the marketplace is added, install the **wellcome** plugin from it.

### 3. Restart the Claude desktop app

Fully quit and reopen it. New plugins only register as slash commands on a fresh session. After restart, typing `/` should list **`/wellcome:create-app`**.

### 4. Smoke test (60 seconds)

1. Open a new, empty folder as the working directory.
2. Type `/wellcome:create-app` — confirm it's recognised and starts the questions.
3. If it doesn't appear, the install didn't land: re-run the two `claude plugin` commands and restart again. If it still doesn't show, confirm the terminal `claude` CLI and the desktop app share the same config root — `claude plugin marketplace list` should include `wellcome-ai`, and `~/.claude/plugins/` should contain a `wellcome` entry.

## What to tell participants on the day

- Don't type `/plugin …` — it won't work in the desktop app, and it's already installed for you.
- Open an empty folder and type `/wellcome:create-app`, then answer the questions in plain English.
- The app it builds runs in your browser straight away — no accounts or keys to set up.
- When you're happy with it, type `/wellcome:publish-app` to put it online and get a link you can share. You'll sign in to Vercel (free) once in your browser — that's the only step you do yourself.

## Keeping the plugin current

When the plugin is updated, refresh machines with:

```bash
claude plugin marketplace update wellcome-ai
claude plugin install wellcome@wellcome-ai
```

then restart the desktop app. Confirm the installed version matches the latest in `.claude-plugin/marketplace.json`.

## Reference: versions

Known-good as of mid-2026:

- Node v24.8.0, npm 11.6.0, git 2.50.1
- Generated app stack: Next.js 16, React 19, shadcn/ui (Base UI components), Tailwind v4
