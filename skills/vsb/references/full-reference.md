# vsb CLI — full reference

Generated against vsb v0.1. The shape of every command, every flag. Use the
sibling `SKILL.md` for the short version + critical rules.

---

## Global options

These work on every command:

| Flag | Description |
|------|-------------|
| `--json` | Force JSON output to stdout. (Auto-on when piped or non-TTY.) |
| `--help` | Citty-rendered command help. With `--json` and *no* command, root prints the full machine-readable command schema. |
| `--api-key <key>` / `--api-key=<key>` | Override the configured key for one call. |

## Environment variables

| Var | Purpose |
|-----|---------|
| `VSB_API_KEY` | Bearer key (issued by `vsb setup` or the in-product CLI Tokens page). |
| `VSB_API_BASE` | Override base URL (default: `https://visualsandbox.com/api`). Useful for staging. |
| `NO_COLOR` | Disable ANSI colors. |

`vsb setup` writes both to `~/.vsb/config.json`. The CLI also auto-loads
`.env` from the working directory unless `--no-auto-load-env` is passed to
`setup`.

---

## `vsb setup`

Configure the CLI. Interactive by default; opens a local callback server +
browser to issue a project-scoped key.

```
vsb setup                                       # interactive wizard
vsb setup --non-interactive --api-key <key>     # script-friendly
vsb setup --output-format auto|json|standard    # set the default mode
vsb setup --no-auto-load-env                    # skip dotenv on future runs
```

---

## `vsb models [query]`

Search/list the model registry.

| Flag | Type | Description |
|------|------|-------------|
| `query` | positional | Free-text search (matches name + description) |
| `--category <c>` | string | Filter by category: `image`, `video`, `audio`, `3d`, `vector`, `text`, `image-enhance` |
| `--modality <m>` | string | Filter by modality: `image`, `video`, `audio`, `text`, `3d`, `vector` |
| `--status <s>` | string | `active` (default), `deprecated`, `all` |
| `--limit <n>` | string | Max rows (default 20) |
| `--cursor <c>` | string | Pagination cursor (returned as `next_cursor`) |
| `--slug a,b,c` | string | Fetch specific slugs directly (comma-separated) |

Returns a `RegistryResponse`:

```json
{
  "total": 42,
  "next_cursor": null,
  "models": [
    {
      "slug": "nano-banana",
      "category": "image",
      "display_name": "Nano Banana",
      "description": "...",
      "owner": "Google",
      "tags": [],
      "inputs_schema": { "..." : "JSONSchema dict" },
      "ui_hints": { "labels": {}, "option_labels": {}, "hidden": [] },
      "default_cost_usd": 0.039,
      "default_cost_unit": "image"
    }
  ]
}
```

Note: `slug` is the **bare name** (`nano-banana`); to *run* the model you need
`<category>/<slug>` (`image/nano-banana`). The `category` field is right next to
the slug in every entry.

---

## `vsb schema <slug>`

Inspect inputs/outputs.

| Flag | Type | Description |
|------|------|-------------|
| `slug` | positional | `<category>/<name>`, e.g. `image/nano-banana` (or `vector/google/gemini-3.1-pro` — names with slashes work) |
| `--format compact|openapi` | string | Default `compact` |

`compact` shape:

```json
{
  "slug": "image/nano-banana",
  "inputs": {
    "prompt": { "type": "string", "required": true, "description": "..." },
    "aspect_ratio": { "type": "string", "enum": ["1:1", "16:9", "..."], "default": "1:1" },
    "image_input": { "type": "array", "default": null, "description": "Reference images" }
  },
  "outputs": {
    "urls": { "type": "array" },
    "format": { "type": "string" }
  }
}
```

`openapi` shape returns the raw OpenAPI 3.1 spec for the endpoint.

---

## `vsb pricing <slug>`

Cost lookup for a model. **Bearer auth required** (API key.)

```json
{
  "slug": "image/nano-banana",
  "currency": "USD",
  "unit": "image",
  "base_cost": 0.039,
  "markup_percent": 25,
  "user_cost_estimate": 0.04875,
  "tiers": [],
  "notes": "Per-image pricing, no resolution surcharge."
}
```

Cost units in v0.1: `image`, `second`, `svg`, `audio`, `model`, `video` (no `per_*` prefix). Some models with variable output use `tiers` (e.g. seconds-based for video).

---

## `vsb run <slug> [--<input> <value>...]`

Execute a model. The CLI walks `argv` collecting every `--<key> <value>` and
`--<key>=<value>` pair into a Pydantic input dict, then POSTs to
`/api/v1/<category>/<name>/`.

| Flag | Description |
|------|-------------|
| `slug` | `<category>/<name>` |
| `--<input> <value>` | Repeat once per model input. Values coerced via `parseValue` — numbers, booleans, JSON literals all work. |
| `--<input>=<value>` | Equivalent equals form. |
| `--async` | Submit + return `{job_id}` without polling |
| `--download [template]` | Save result media after completion. Template tokens: `{request_id}`, `{index}`, `{ext}`. Default: `./{request_id}_{index}.{ext}` |

Reserved (NOT forwarded as inputs): `--async`, `--json`, `--help`, `--download`, `--logs`, `--api-key`.

Sync return on success:

```json
{
  "status": "completed",
  "slug": "image/nano-banana",
  "job_id": "8f3...",
  "cost": 0.04875,
  "result": { "urls": [...], "format": "jpg", ... },
  "downloaded_files": ["./out/8f3..._0.jpg"]
}
```

Async return:

```json
{
  "status": "submitted",
  "job_id": "8f3...",
  "slug": "image/nano-banana",
  "hint": "vsb status 8f3..."
}
```

---

## `vsb status <job_id>`

Poll a job. By default returns `JobOut` as-is.

| Flag | Description |
|------|-------------|
| `jobId` | positional |
| `--result` | Include the full `result` blob (urls, cost, dimensions). Implied by `--download`. |
| `--cancel` | POST to `/v1/jobs/<id>/cancel/`. Returns the cancelled job; exits 1 with a clear message if it had already finished (409). |
| `--download [template]` | Download result media when status is `completed`. Same templating as `vsb run`. |

`JobOut`:

```json
{
  "job_id": "...",
  "category": "video",
  "slug": "veo-3.1-fast",
  "status": "queued | in_progress | completed | failed | cancelled",
  "progress": 42,
  "created_at": "...",
  "completed_at": "...",
  "result": null | { "urls": [...], "format": "mp4", "cost": 0.45, "raw_cost": 0.36, "predict_time": 32.1, "width": 1920, "height": 1080 },
  "error": null | { "code": "...", "message": "...", "retry_after": null, "details": null },
  "metadata": { "external_id": "...", "provider": "fal", "queued_at": "...", "started_at": "...", "cancelled_at": null },
  "parent_uuid": null
}
```

---

## `vsb upload <path-or-url>`

Upload a local file or remote URL to Visual Sandbox storage. Returns the
hosted CDN URL — pass that as an input to subsequent `vsb run` calls.

```json
{
  "status": "uploaded",
  "url": "https://cdn.visualsandbox.com/.../file.jpg",
  "asset_id": "uuid",
  "mime": "image/jpeg",
  "bytes": 234567,
  "filename": "photo.jpg",
  "source": "user_upload",
  "created_at": "..."
}
```

Allowed types: `image/*`, `video/*`, `audio/*`, `model/gltf*`. Anything else → 400.

---

## `vsb presets`

Five subcommands. See the `presets` skill for the deep dive.

| Command | Purpose |
|---------|---------|
| `vsb presets list [--limit <n>]` | List your presets |
| `vsb presets get <uuid>` | Full preset (inputs, refs, examples) |
| `vsb presets run <uuid> [--input k=v]... [--inputs-json '{...}'] [--async]` | Re-run a preset, optionally overriding inputs |
| `vsb presets create --from <preset.json>` | Create from a `PresetIn` JSON file |
| `vsb presets delete <uuid>` | Delete (returns exit 1 + clean message on 404) |

Override semantics for `run`:
- Base = `preset.inputs`
- `--input key=value` (repeatable) merges on top
- `--inputs-json '{...}'` *replaces* base entirely (escape hatch)
- `preset.refs` (file fields) hydrate any missing keys

---

## `vsb skills`

| Command | Purpose |
|---------|---------|
| `vsb skills list [query]` | List bundled skills, optionally filter by query |
| `vsb skills install <name> [--force]` | Copy SKILL.md + references into `./.claude/skills/<name>/` |
| `vsb skills update [name]` | Re-copy any skill whose bundled sha256 differs from installed |
| `vsb skills remove <name>` | Delete from `.claude/skills/<name>/` and the manifest |

A manifest tracks installs at `./.agents/skills/.installed.json` (sha256 per file) so `update` is precise.

v0.1 only writes to `.claude/skills/`. Cursor + AGENTS.md targets land in v0.2.

## `vsb init`

One-shot install of the default skill bundle (`vsb`, `presets`, `image`, `video`, `audio`) into the current project. Equivalent to running `vsb skills install` for each.

| Flag | Description |
|------|-------------|
| `--force` | Reinstall even if a skill is already present |
