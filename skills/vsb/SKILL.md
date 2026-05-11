---
name: vsb
description: >
  Use the vsb CLI to generate AI media via Visual Sandbox — image, video,
  audio, 3D, vector. Trigger when the user mentions "vsb", "Visual Sandbox CLI",
  "generate an image/video/audio", "run a model", or asks to produce media via
  shell. Always pipe through --json when output will be parsed by an agent.
---

# vsb CLI

`vsb` is the Visual Sandbox command-line tool. Pretty output for humans, structured JSON for agents. Every successful run charges the configured account — costs are real.

For full reference (every command, every flag, every exit code), see
[references/full-reference.md](references/full-reference.md).

## Critical rules (read first)

1. **Always pass `--json` when an agent will read the output.** Pretty mode is human-only and is unstable across versions. (Output also auto-switches to JSON when stdout is piped — but be explicit.)
2. **Never invent slugs.** Use `vsb models "<query>" --json` to discover, then `vsb schema <category>/<slug> --json` to verify before `vsb run`.
3. **Slugs are `<category>/<name>`.** Examples: `image/nano-banana`, `video/veo-3.1`, `audio/elevenlabs-sound-fx`. Some names contain a slash (`vector/google/gemini-3.1-pro`) — keep them as one token.
4. **Inspect schema before running.** `vsb schema <slug> --json` shows exact field names, types, defaults, enums, and which are required.
5. **Use `--download` for files**, never `curl`. The CLI handles auth headers, redirects, and naming templates. Downloads after a completed job; templates support `{request_id}`, `{index}`, `{ext}`.
6. **Image is sync, video and audio are async.** Image runs typically finish in ~5–10s — `vsb run image/...` blocks fine. Video/audio can take 30s–3min — use `--async`, then poll with `vsb status <job_id> --result --download <template>`.
7. **Estimate cost first.** `vsb pricing <category>/<slug> --json` returns `user_cost_estimate`. Show it to the user before running expensive video models.
8. **Auth.** Run `vsb setup` once — opens a browser to issue an API key, writes it to `~/.vsb/config.json`. Or set `VSB_API_KEY` in the env / `.env`. `vsb pricing` and most write endpoints require auth.
9. **Every `vsb run` auto-attaches to the user's live sandbox.** Each completed generation becomes a draggable node on `https://visualsandbox.com/sandbox/`. To opt out for a one-off script, pass `--no-sandbox`. To target a non-default sandbox, pass `--sandbox-uuid <uuid>`.
10. **Selection-aware prompts.** When the user's request references "this", "him", "the image", "selected", "that one" — or anything that implies a subject already on screen — call `vsb sandbox selection --json` first. Returns the node(s) the user has selected on the canvas: prompt, model, output URL. Pass the `output_url` as the input image to the next `vsb run` (e.g. `--image_urls "[\"<url>\"]"` for nano-banana). If selection is empty, ask the user to click a node before continuing.

## Command index

| Command | Purpose |
|---------|---------|
| `vsb setup` | Configure API key (browser flow) + output preferences |
| `vsb models [query]` | Search the catalog (filter by `--category`, `--modality`, `--status`) |
| `vsb schema <slug>` | Inspect inputs/outputs (`--format compact|openapi`) |
| `vsb pricing <slug>` | Cost lookup (Bearer auth required) |
| `vsb run <slug> --<input> <value>` | Execute a model — sync by default, `--async` to queue |
| `vsb status <job_id>` | Poll a job (`--result`, `--cancel`, `--download [template]`) |
| `vsb upload <path-or-url>` | Upload local file or remote URL to VS CDN |
| `vsb presets <list|get|run|create|delete>` | Manage saved model+inputs presets |
| `vsb sandbox selection` | Read what the user has selected on the canvas (prompt, model, image URL) |
| `vsb skills <list|install|update|remove>` | Manage agent skill packs in `.claude/skills/` |
| `vsb init` | One-shot install of the default skill bundle |

## JobOut shape (what `run` and `status` return)

```json
{
  "job_id": "8f3...",
  "category": "image",
  "slug": "nano-banana",
  "status": "queued|in_progress|completed|failed|cancelled",
  "progress": null,
  "created_at": "2026-05-10T14:00:00Z",
  "completed_at": "2026-05-10T14:00:08Z",
  "result": {
    "urls": ["https://cdn.visualsandbox.com/.../out.jpg"],
    "format": "jpg",
    "width": 1024,
    "height": 1024,
    "cost": 0.04875,
    "raw_cost": 0.039,
    "predict_time": 5.167
  },
  "error": null
}
```

On failure, `result` is `null` and `error` is `{code, message, retry_after, details}`. Status `"in_progress"` is the same as `"processing"` — both mean "still running."

## Quick patterns

### 1. Fast image (sync + download)

```bash
vsb run image/nano-banana \
  --prompt "a calico cat in a sunlit kitchen" \
  --aspect_ratio 16:9 \
  --download "./out/{request_id}.{ext}" \
  --json
```

### 2. Discover when the user gives a fuzzy task

```bash
# "Make me a video of a tiger"
vsb models "video" --modality video --limit 5 --json | jq '.models[] | {slug, category, display_name}'
vsb schema video/veo-3.1 --json | jq '.inputs'
```

### 3. Async + poll (video / audio)

```bash
JOB=$(vsb run video/veo-3.1-fast --prompt "a tiger walking through tall grass" --async --json | jq -r '.job_id')

# Poll yourself, or just call status with --result --download which waits-by-fetching:
vsb status "$JOB" --json | jq '.status'
# When status == "completed":
vsb status "$JOB" --result --download "./out/{request_id}.{ext}" --json
```

### 4. Upload a local image, then edit it

```bash
URL=$(vsb upload ./photo.jpg --json | jq -r '.url')
vsb run image/nano-banana \
  --prompt "make the sky stormy" \
  --image_input "[\"$URL\"]" \
  --download "./out/" \
  --json
```

`image/nano-banana` takes a reference-image array (currently `image_input`). Pass it as a JSON literal — the CLI's `--key value` parser auto-coerces JSON. Other models name the field differently — never assume; the CLI rejects unknown flags with a "Did you mean…?" hint, but `vsb schema <slug>` is the fastest source of truth.

### 5. Cancel a runaway job

```bash
vsb status "$JOB" --cancel --json
```

### 6. Act on the user's canvas selection

When the user says "make him purple", "remove the background", "stylize this" — they're pointing at a node already on the canvas. Read the selection first, then chain it into the next run:

```bash
SEL=$(vsb sandbox selection --json)
URL=$(echo "$SEL" | jq -r '.nodes[0].generation.output_urls[0] // .nodes[0].media_asset.url')
[ -z "$URL" ] && { echo "Select a node on the canvas first." >&2; exit 1; }

vsb run image/nano-banana \
  --prompt "make subject purple" \
  --image_input "[\"$URL\"]" \
  --json
```

Pull the original prompt out of the selection (`.nodes[0].generation.prompt`) when you want to refine vs replace ("same scene, but at night"). The result lands back on the canvas next to the original.

### 7. Recover the result URLs of a past job

If you forgot to capture the output URLs (or the JSON got truncated by a shell
pipe), refetch them by job id:

```bash
vsb status <job_id> --result --json | jq '.result.urls'
```

`--result` blocks if the job is still running; for an instant peek skip it and
inspect `.status` first.

## Exit codes (for shell scripting)

| Code | Meaning |
|------|---------|
| 0    | Success |
| 1    | Generic failure (job failed, server error) |
| 2    | Validation error (bad slug, missing required input, bad flag) |
| 3    | Auth error (no API key, expired, wrong scope) |
| 4    | Insufficient credits |
| 5    | Provider overloaded / rate-limited |
| 124  | Job cancelled |
| 130  | User interrupt (Ctrl-C) |

`vsb` always writes errors to stderr as JSON when `--json` is set, so a wrapping agent can read both stdout and stderr structured.

## Slug families to know (verify with `vsb models --json`)

- **Image** (sync, ~5–10s): `image/nano-banana`, `image/nano-banana-2`, `image/nano-banana-pro`, `image/gpt-image-2`
- **Image enhance**: `image-enhance/recraft-remove-background`, `image-enhance/upscale`
- **Video** (always async, 30s–3min): `video/seedance-2`, `video/seedance-2-fast`, `video/kling-v3-motion-control`, `video/veo-3.1`, `video/veo-3.1-fast`
- **Audio** (async, 5–30s): `audio/elevenlabs-sound-fx`
- **3D**: `3d/hunyuan-3d-3.1`
- **Vector**: `vector/google/gemini-3.1-pro` (slashed name), `vector/quiver-arrow-1.1`
- **Text**: `text/openrouter-chat`

For per-modality recipes, see the sibling skills: `image`, `video`, `audio`, `presets`.
