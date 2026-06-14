# Running a build day — facilitator setup

**For the course runner, not participants.** This is the prep checklist for whoever sets up the room before an accelerated build day. Participants don't install anything — they just open a folder and run `/wellcome:create-app` on the day. Your job is to make sure that command already works on every machine.

## Why pre-setup is required

As of mid-2026 the Claude **desktop app does not support the `/plugin` command** — typing it returns *"/plugin isn't available in this environment."* So participants can't install the plugin from inside the app. You pre-install it from a terminal (which writes to the shared `~/.claude/plugins` config the desktop app reads), and the command then appears in the desktop app after a restart. The marketplace repo is public, so no GitHub login is needed.

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

### 2. Install the plugin (terminal)

```bash
claude plugin marketplace add wellcome-ai/marketplace
claude plugin install wellcome@wellcome-ai
```

Expect `✔ Successfully added marketplace: wellcome-ai` and `✔ Successfully installed plugin: wellcome@wellcome-ai`.

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
