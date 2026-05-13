# Visual Sandbox Skills

Agent skills for [Visual Sandbox](https://visualsandbox.com) — drop-in knowledge packs that teach Claude Code (and other agent runtimes) how to generate AI media via the `vsb` CLI.

Each skill is a single `SKILL.md` (plus optional `references/`) with YAML frontmatter that the agent runtime reads to decide when the skill applies.

## Skills

| Skill | Purpose |
|-------|---------|
| [`vsb`](skills/vsb/) | Top-level guide to the `vsb` CLI — auth, run, status, presets, agent rules |
| [`image`](skills/image/) | Pick the right image model and prompt it well |
| [`video`](skills/video/) | Generate / edit video — async + status polling |
| [`audio`](skills/audio/) | Generate sound effects, ambient audio, speech |
| [`presets`](skills/presets/) | Run, save, override, and share reproducible model configurations |
| [`nano-banana`](skills/nano-banana/) | Prompt the Nano Banana family well — edit-mode rules, text rendering, multi-reference blending |
| [`image-prompting`](skills/image-prompting/) | Canonical prompt-craft trunk for every image model — anatomy, reference keep/ignore rules |
| [`ugc-people`](skills/ugc-people/) | UGC-style single-person ad photos — selfie POV + talking-head formats for 9:16 TikTok/Reels |

## Install

```bash
# One-shot install of the binary + default skill bundle + auth
curl -fsSL https://visualsandbox.com/install.sh | sh
vsb setup
```

`vsb setup` writes the default bundle (`vsb`, `presets`, `image`, `image-prompting`, `video`, `audio`) into `~/.claude/skills/` so Claude Code finds them in every project. Pass `--project` to install into `./.claude/skills/` instead (committable, per-project, shared via git).

```bash
# Skills only — skip auth (re-run after editing locally)
vsb setup --skills-only

# Pick individual skills
vsb skills install nano-banana
vsb skills install ugc-people --project

# Refresh installed skills to the latest version baked into the binary
vsb skills update
```

Agents working with `vsb` should follow [`cli-install.md`](cli-install.md) for the full install playbook — covers PATH issues, token recovery, and the first-run verify.

## Layout

```
skills/
├── <name>/
│   ├── SKILL.md          # frontmatter + body
│   └── references/        # optional supporting docs
├── index.json             # sha256 + bytes per file (generated)
```

## How the CLI consumes this

`vsb` embeds these skills directly into the binary at build time — every
release ships a snapshot of `skills/`, sha256-verified against `index.json`.
`vsb setup` / `vsb skills install` write from the embedded bundle with
zero network calls.

Power users can opt into the live registry instead of the embedded copy:

```bash
vsb skills install nano-banana --remote
# or for the whole session:
VSB_SKILLS_REMOTE=1 vsb setup --skills-only
```

Remote mode pulls from `https://raw.githubusercontent.com/vladartym/vsb-skills/main/skills/`,
caches in `~/.vsb/skills-cache/`, and verifies sha256 against the live `index.json`.
Pin to a tag, branch, or sha with `VSB_SKILLS_REF=v0.3 vsb setup --skills-only`.

## Contributing

Edits land in `skills/<name>/SKILL.md`. After editing locally:

```bash
bun run gen
```

That regenerates `skills/index.json` with fresh sha256s. The CI workflow
in `.github/workflows/index.yml` also regenerates the index on every
push to `main`, so manual runs are mainly for previewing the diff
before opening a PR.

## License

MIT — see [LICENSE](LICENSE).
