---
name: image
description: >
  Pick the right Visual Sandbox model for any image task and prompt it well.
  Trigger when the user wants to generate, edit, restyle, upscale, or remove
  the background from images via the vsb CLI. All execution goes through
  `vsb run image/<slug>` — sync, ~5–10s for the standard models. Always
  verify slugs with `vsb models --modality image --json` first.
---

# Image generation with vsb

Image runs are sync — `vsb run image/<slug>` blocks until done (typically
5–10s). No `--async` needed. Pipe `--json` and `--download "<template>"` to
capture results.

## Picking a model

Verify the live catalog with `vsb models --modality image --json | jq '.models[] | {slug, category, owner}'`.

| Task | Default slug | Why |
|------|--------------|-----|
| Fast text-to-image / edit | `image/nano-banana` | Google. Cheapest, fastest, ~5s. Handles both text-to-image AND image edits — the same endpoint switches modes when a reference image array is non-empty. |
| Higher quality / 2× output | `image/nano-banana-2` | Same shape, better fidelity. |
| Top-tier Google output | `image/nano-banana-pro` | Most expensive of the family, sharpest detail. |
| Photoreal / text rendering / multi-ref edit | `image/gpt-image-2` | OpenAI. Best for legible typography, posters, product mockups. Slower + pricier than Nano Banana. |
| Background removal | `image-enhance/recraft-remove-background` | Note: category is `image-enhance`, not `image`. |
| Upscale | `image-enhance/upscale` | Same — `image-enhance` category. |

## Prompt patterns

### Nano Banana family (text-to-image)

```bash
vsb run image/nano-banana \
  --prompt "a calico cat curled on a sunlit windowsill, soft morning light, shallow depth of field" \
  --aspect_ratio 16:9 \
  --output_format jpg \
  --download "./out/{request_id}.{ext}" \
  --json
```

Nano Banana likes concrete nouns + lighting + camera language. Aspect ratios
(`1:1`, `16:9`, `3:2`, `4:3`, `5:4`, `4:5`, `3:4`, `2:3`, `9:16`, `21:9`) are
strict enums — use `vsb schema image/nano-banana --json` to confirm before
passing.

### Nano Banana (image edit — multi-ref)

```bash
URL1=$(vsb upload ./photo.jpg --json | jq -r '.url')
URL2=$(vsb upload ./reference.jpg --json | jq -r '.url')

vsb run image/nano-banana \
  --prompt "place the cat from image 1 onto the windowsill from image 2, match lighting" \
  --image_input "[\"$URL1\",\"$URL2\"]" \
  --aspect_ratio match_input_image \
  --download "./out/" \
  --json
```

Pass JSON-array inputs as a literal — `parseValue` auto-coerces. The exact
field name (e.g. `image_input`) is whatever `vsb schema image/nano-banana
--json` returns; never guess. `vsb run` pre-validates flags against the
schema and rejects unknown keys with a "Did you mean…?" hint before any
charge is incurred.

`aspect_ratio: "match_input_image"` keeps the dimensions of the first
reference.

You can pass server-side paths directly too — any `/media/predictions/...`
URL from a prior `vsb run` works without re-uploading.

### GPT Image 2 (typography, posters, mockups)

```bash
vsb run image/gpt-image-2 \
  --prompt "minimal poster: bold serif headline 'NORTHWIND' on a kraft paper background, registration marks in the corners" \
  --quality high \
  --aspect_ratio 3:4 \
  --output_format png \
  --download "./out/{request_id}.png" \
  --json
```

GPT Image 2 specifically excels when you need **legible, multi-line text** baked
into the image. Quality enum is `auto|low|medium|high`. `n` (1–4) gives
multiple variants in one call.

### Background removal

```bash
URL=$(vsb upload ./product-photo.jpg --json | jq -r '.url')
vsb run image-enhance/recraft-remove-background \
  --image "$URL" \
  --download "./out/cutout.png" \
  --json
```

Note the category is `image-enhance`, not `image`, and `recraft-remove-background`
+ `upscale` both take a single string field (currently `image`) — different from
Nano Banana's array. Always run `vsb schema image-enhance/<slug> --json` first
to confirm field names; the CLI rejects unknown flags with a "Did you mean…?"
hint before submitting.

## Cost estimation

```bash
vsb pricing image/nano-banana --json | jq '.user_cost_estimate'
# 0.04875 → roughly 5 cents per image
```

Nano Banana family is cents-per-image. GPT Image 2 is ~$0.05–0.20 depending on
quality. Background removal and upscale are sub-cent.

## Common gotchas

- **Input field names vary by model** — Nano Banana uses an array (currently `image_input`), most enhance models take a single string (currently `image`), GPT Image 2 has its own shape. `vsb run` pre-validates against the schema, so just `vsb schema <slug>` once and copy the exact field name.
- **`aspect_ratio: "match_input_image"` only makes sense in edit mode** (when reference images are set). For text-to-image alone it falls back to a default.
- **`output_format` defaults to `jpg`.** If you want transparent PNG (e.g. for compositing), set `--output_format png` and remember the alpha channel is only meaningful for edit/cutout flows.
- **Don't loop `vsb run` inside a tight shell loop** — use a small batch (≤5 in parallel via `&`) to respect the provider's rate limit.
