---
name: vsb-cli-install
description: Use when the user asks you to install Visual Sandbox CLI (`vsb`), set it up, configure their API key, or get them ready to run AI media generation via the shell. Walks the install → auth → verify loop end-to-end and recovers from the common failure modes (browser blocked, missing PATH, expired token).
---

# Install the vsb CLI

`vsb` is the Visual Sandbox command-line tool. Pretty output for humans, structured JSON for agents. This file is the agent playbook for first-time install. For day-to-day usage, read the `vsb` skill after install completes.

## Install prompt contract

You are installing on the user's real machine. Be explicit before you run anything: tell the user what you are about to install and where. Never modify shell config without consent. The user's terminal is the only thing that proves a step worked — re-screenshot, re-`vsb version`, re-`vsb status` after each step.

## Steps

### 1. Install the binary

macOS or Linux:

```bash
curl -fsSL https://visualsandbox.com/install.sh | sh
```

Windows: not yet supported. If the user is on Windows, recommend WSL2 + the curl command above.

Override the install dir with `VSB_INSTALL_DIR` (default `~/.local/bin`). Override the version with `VSB_VERSION=v0.2.0` (default `latest`).

Verify the binary is on the user's PATH:

```bash
command -v vsb && vsb version
```

If `command -v vsb` is empty, the install dir (typically `~/.local/bin`) is not on `$PATH`. Tell the user the exact line to add to their shell rc and ask them to source it. **Do not edit shell rc files yourself.**

### 2. Configure auth + install agent skills

```bash
vsb setup
```

What this does, in order:

1. Installs the default agent skill bundle into `~/.claude/skills/` so Claude Code finds it in every project. Pass `--project` to install into `./.claude/skills/` instead (committable, per-project, shared via git).
2. Opens a browser to `visualsandbox.com/account/cli-tokens/` for the user to authorize the CLI. The browser callback writes the token to `~/.vsb/config.json`.
3. Validates the token against the live API.
4. Saves output-format + auto-load-env preferences.

**If the browser does not open**, tell the user to copy the URL `vsb setup` printed into their browser manually. Do not retry without consent.

**If the user already has a token** (`/account/cli-tokens/`), they can skip the browser by picking "Paste an existing token" at the prompt.

**Headless / SSH / CI**: use `vsb setup --non-interactive --api-key <key>`. Skips both prompts and the browser. The user must already have a token.

### 3. Verify

```bash
vsb models "image" --json | head -c 200
```

If you see a JSON object with `models: [...]`, the install + auth + network all work. If you see an HTTP 401, the token is invalid — rerun `vsb setup`.

### 4. First real run (optional, costs ~$0.04)

```bash
vsb run image/nano-banana --prompt "a small test image" --json
```

Returns `{job_id, status: "completed", result: {urls: ["https://cdn..."]}}` if everything works. Confirms the full pipeline (auth + balance + provider + sandbox attach) is healthy.

Tell the user the cost before running. Skip this step if the user has no balance loaded — direct them to `visualsandbox.com/account/billing/` to add funds first.

## Recovery rules

- **`401 Unauthorized` from `/api/v1/...`**: token rotated or expired. Run `vsb setup` again.
- **`402 Payment Required` / "insufficient balance"**: open `visualsandbox.com/account/billing/`. Tell the user the current cost from `vsb pricing <slug>`.
- **`Skills index failed to load`** during `vsb setup`: the embedded bundle is broken (rare — only happens on a corrupted binary). Reinstall via the curl line in step 1.
- **`vsb: command not found`** after curl install: PATH is not set. The install script prints the exact `export PATH=...` line — copy that to the user's shell rc.
- **Browser flow times out (180s)**: the user did not finish authorizing. Re-run `vsb setup` and use the paste-token method if browser auth fails again.

## What gets installed where

| Path | Purpose |
|------|---------|
| `~/.local/bin/vsb` | The binary (override with `VSB_INSTALL_DIR`) |
| `~/.vsb/config.json` | API key + preferences |
| `~/.claude/skills/<name>/` | Agent skills (global default; `--project` for `./.claude/skills/`) |
| `~/.claude/skills/.installed.json` | Skill manifest — tracks installed version, sha256, source |

## After install

Read the `vsb` skill (now at `~/.claude/skills/vsb/SKILL.md`) before running any model. It covers the critical rules: `--json` for agents, slug discovery via `vsb models`, schema inspection before `vsb run`, sync vs async, sandbox auto-attach, selection-aware prompts. Also see `image`, `video`, `audio`, `presets`, and the prompt-craft trunks.
