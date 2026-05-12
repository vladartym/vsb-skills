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
# One-shot install of the default bundle into ./.claude/skills/
vsb init

# Or pick individual skills
vsb skills install vsb
vsb skills install nano-banana

# Update installed skills to the latest bundled version
vsb skills update
```

`vsb init` and `vsb skills install` write into `.claude/skills/<name>/` of the current project. Claude Code auto-loads any skill there based on the frontmatter `description`.

## Layout

```
skills/
├── <name>/
│   ├── SKILL.md          # frontmatter + body
│   └── references/        # optional supporting docs
├── index.json             # sha256 + bytes per file (generated)
```

## How the CLI consumes this

The `vsb` CLI fetches `skills/index.json` from
`https://raw.githubusercontent.com/vladartym/vsb-skills/main/skills/index.json`
on demand, then pulls each listed file and verifies sha256. Results are
cached in `~/.vsb/skills-cache/`. Power users can pin to a tag, branch,
or sha with `VSB_SKILLS_REF=v0.3 vsb init`.

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
