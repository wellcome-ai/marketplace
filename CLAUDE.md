# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Claude Code plugin marketplace. There is no build, test, or lint step — the repo is a few declarative files plus a README and facilitator docs.

```
.claude-plugin/marketplace.json               marketplace catalog
plugins/wellcome/.claude-plugin/plugin.json   plugin manifest
plugins/wellcome/skills/create-app/SKILL.md   skill body (where the logic lives)
README.md                                     user-facing intro
docs/build-day-setup.md                       facilitator setup guide
```

The plugin's audience is **non-technical participants on Wellcome AI's accelerated build days**. Every editorial choice in SKILL.md serves them, not engineers — keep that in mind when editing copy.

## Naming hierarchy — easy to break, hard to debug

Six distinct names assemble into the three commands users type. Get one wrong and either the install fails or commands appear under the wrong namespace.

| Name | Lives in | Current |
|---|---|---|
| GitHub org | URL | `wellcome-ai` |
| Repo | URL | `marketplace` |
| Marketplace name | `marketplace.json` → `name` | `wellcome-ai` |
| Plugin name | `plugin.json` → `name` AND `marketplace.json` → `plugins[].name` | `wellcome` |
| Plugin folder | `plugins/<here>/` (referenced by `plugins[].source`) | `wellcome` |
| Skill name | `skills/<here>/` (no `name` field exists in SKILL.md frontmatter) | `create-app` |

Assembles to:
```
/plugin marketplace add wellcome-ai/marketplace
/plugin install wellcome@wellcome-ai
/wellcome:create-app
```

**Hard constraint**: the plugin's `name` in `plugin.json` must equal its `name` in the `plugins[]` array of `marketplace.json`. Mismatch installs cleanly but commands appear under the wrong prefix.

Renaming the plugin requires four coordinated changes: `plugin.json` → `name`, `marketplace.json` → `plugins[].name`, `marketplace.json` → `plugins[].source` path, and the actual folder rename.

## SKILL.md — design constraints to preserve

The skill body is the actual product. When editing it, preserve these:

1. **Make decisions, don't ask.** The opening "Operating mode" section instructs Claude to never ask the user technical questions. Adding a step? Follow the same stance — pick a default and proceed.

2. **Phase structure**:
   - **Phase 1 (Intake, Q1–Q8)**: Q1/Q2/Q8 are free-text plain conversation; Q3–Q7 use `AskUserQuestion`.
   - **Phase 1.5 (Q8 intent parsing)**: a keyword table maps free-text Q8 mentions to auto-installed tools. **This is the extension surface** — to add a new auto-detected tool, add a row to the table AND a conditional install block in step 3j, AND a success criterion in step 3l.
   - **Phase 2 (Confirm)**: summary + go-ahead.
   - **Phase 3 (Build)**: scaffold (3a–3c) → conditional services (3d–3j) → README (3k) → autonomous app build (3l) against numbered success criteria → final report (3m).

3. **Git is silent.** Every phase commits, but Claude never narrates it. Preserve "Never mention git" when adding steps.

4. **Success criteria drive the build.** Step 3l iterates until all numbered criteria hold. Add a service → add a criterion, or the iteration loop won't know when to stop on that service.

5. **Stack lock-ins** (don't change without strong reason): Node + npm (not Bun, not pnpm — broadest AI training coverage); Next.js App Router only; Server Components by default; conventional commit messages.

When inserting a step in Phase 3, the section letters (3a–3m) cascade. The "Stop conditions" block has an in-document reference to `3m` (the final report) that must update if Phase 3 grows.

## Testing changes locally

```
/plugin marketplace add /Users/rich/repos/wellcome-cc-marketplace
/plugin install wellcome@wellcome-ai
/wellcome:create-app    # run in a throwaway directory — the skill scaffolds a new app there
```

## See also

`README.md` for the user-facing intro, install commands, the stack, and what `/wellcome:create-app` asks.
