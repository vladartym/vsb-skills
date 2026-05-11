---
name: nano-banana
description: >
  Prompt the Nano Banana family (Google's Gemini Image — `image/nano-banana`,
  `image/nano-banana-2`, `image/nano-banana-pro`) well. Trigger whenever the user
  is about to call `vsb run image/nano-banana*` or asks how to phrase a prompt
  for these models — covers prompt anatomy, edit-mode rules, text rendering,
  multi-reference blending, camera/lighting vocabulary, and the things this
  family gets wrong.
---

# Prompting Nano Banana

Nano Banana is built on Gemini 3 — it reasons over your prompt before drawing,
so descriptive natural language beats keyword soup. The same endpoint handles
text-to-image AND edits: pass `image_input` (an array) to switch into edit mode.

Verify the live schema first: `vsb schema image/nano-banana --json`.

## The four core rules

1. **Be specific.** Concrete subject, lighting, composition. "A dog in a park"
   gives generic output. "A golden retriever sitting on a bench in a Japanese
   cherry blossom garden at sunset" gives a direction.
2. **Positive framing only.** Describe what you want, not what you don't.
   "Empty cobblestone street" works; "a street with no cars" doesn't.
3. **Use photography + film language.** `low angle`, `aerial view`,
   `golden hour`, `f/1.8 shallow depth of field`, `three-point softbox`. The
   model maps these to real visual transformations.
4. **Iterate conversationally.** Don't aim for a one-shot perfect image — get a
   first version, then refine with follow-up prompts that say what stays and
   what changes.

## Prompt anatomy (text-to-image)

A strong prompt is a scene description, not a tag list. Cover these slots:

| Slot          | Example fragment |
|---------------|------------------|
| Subject       | "a stoic robot barista with glowing blue optics" |
| Action        | "brewing a single pour-over coffee" |
| Location      | "a futuristic café on Mars at golden hour" |
| Composition   | "low-angle medium shot, 9:16 vertical" |
| Camera/lens   | "Shot on Fujifilm X-T5, 35mm lens, f/1.8 shallow depth of field" |
| Lighting      | "warm rim light from the window, soft fill from above" |
| Style         | "photoreal, cinematic colour grading, muted teal tones" |
| Materiality   | "brushed steel apron, matte ceramic mug, navy blue tweed" |

You don't need every slot for every image — pick the ones that matter for the
shot. The more concrete each one is, the closer the output lands.

## Picking a variant

| Slug | When to use |
|------|-------------|
| `image/nano-banana` | Default. Fastest + cheapest (~5s, ~$0.05). Great for iterating and for everyday image-to-image edits. |
| `image/nano-banana-2` | Same shape, higher fidelity. Use when stills aren't crisp enough on the first model. |
| `image/nano-banana-pro` | 4K-capable, sharpest text + typography, best for posters, infographics, packaging mockups, and editorial work. Slowest + priciest of the three. |

All three share the same `image_input` array, same `aspect_ratio` enum, same
`output_format` enum. Confirm with `vsb schema` before assuming.

## Image edits (`image_input` set)

When `image_input` is non-empty, the model is editing — your prompt should lead
with the change and be explicit about what stays the same.

**Good:** `"Change the man's tie to dark green. Keep the rest of the photo,
lighting, and composition exactly as they are."`

**Bad:** `"man with green tie"` (loses every other detail).

Edit-mode patterns that work:

- **Add / remove / swap:** `"Remove the car in the background."` `"Replace the
  coffee mug with a vintage typewriter."`
- **Restyle:** `"Restyle as a 1990s film photograph — kodak gold, slight grain,
  warm cast."` `"Convert to ink-on-paper line art."`
- **Reframe / re-angle:** `"Re-render this scene from a low angle, keeping the
  same subject and lighting."`
- **Weather / time-of-day:** `"Same scene but at golden hour with backlit
  rim light."`
- **Product extraction:** `"Extract the mug and place it on a clean white
  studio backdrop with a soft shadow."`
- **Multi-image blend** (up to 14 refs — Pro best): name each reference and
  give it a role. `"Use Image A for the subject's pose, Image B for the art
  style, and Image C for the background environment."`

For the `aspect_ratio` field, `match_input_image` only makes sense in edit
mode — it keeps the first reference's dimensions.

## Text rendering

Nano Banana is one of the few image models that renders legible text. Pro is
the sharpest of the three.

- **Quote the exact words.** `"The headline 'URBAN EXPLORER' rendered in
  bold, white, sans-serif font at the top of the poster."`
- **Describe the font.** Style words work (`bold`, `cursive`, `serif`,
  `monospace`, `neon signage`), and named fonts work surprisingly often
  (`Century Gothic`, `Helvetica`-style).
- **Localize.** Write the prompt in any language and pass the target text in
  quotes. For exotic scripts give the model the literal characters — don't
  ask it to translate.
- **Text-first hack for tricky layouts.** Two-step: first ask Nano Banana
  (chat-style) for the literal text content, then run a generation request
  that quotes that text. Improves accuracy on long strings.

## Character + product consistency

- Give each character/object a distinct name in the prompt — the model uses
  the name as an anchor across edits.
- For consistent characters, upload a "reference pack" (multiple angles of
  the same person/object) and refer to them by name.
- Pro supports up to 14 reference images per prompt; the standard model is
  best with 1–3.

## Camera + lighting vocabulary that works

Treat yourself as a creative director, not a tag-stuffer:

- **Cameras:** "Shot on iPhone 17 Pro Max", "Shot on Fujifilm X-T5",
  "cheap disposable film camera" (raw nostalgic flash look), "RED cinema
  camera" (filmic).
- **Lenses:** `f/1.8 shallow depth of field`, `wide-angle 24mm`, `macro lens`,
  `tilt-shift`, `telephoto compression`.
- **Lighting setups:** `three-point softbox`, `Rembrandt lighting`,
  `rim-lit from behind`, `golden hour backlight`, `neon ambient at night`.
- **Colour grading:** `muted teal-and-orange Hollywood grade`, `bleach-bypass`,
  `kodachrome warm cast`, `cool monochrome`.
- **Materiality:** name fabrics, finishes, surfaces explicitly. `navy blue
  tweed` > `suit`. `brushed aluminum laptop` > `laptop`. `ornate elven
  plate armor etched with silver leaf` > `armor`.

## What this family gets wrong (known limits)

- **Tiny / dense text.** Long paragraphs at small size will hallucinate
  glyphs. Pro is best; even Pro isn't perfect.
- **Factual diagrams.** Anatomy, scientific cross-sections, and infographics
  may be confidently wrong. Verify any data-driven visual yourself.
- **Counts > ~6.** Asking for "12 cupcakes" often returns 8 or 14. Ask for
  the count + describe the arrangement ("two rows of six").
- **Compound edits.** "Change the sky AND swap the outfit AND remove the
  car" in one shot drifts — do them as a 3-turn conversation.
- **Multilingual grammar.** Non-English text may have small grammatical
  slips or cultural mismatches. Provide the literal target string in quotes
  when accuracy matters.

## Worked examples

### Photoreal portrait (text-to-image)

```bash
vsb run image/nano-banana \
  --prompt "A 28-year-old florist standing in her shop doorway at golden hour. She wears a denim apron over a cream linen shirt. Shot on Fujifilm X-T5, 35mm, f/1.8 shallow depth of field. Warm rim light from the right, soft fill from the open window. Photoreal, cinematic muted teal-and-orange grading." \
  --aspect_ratio 4:5 \
  --output_format jpg \
  --download "./out/{request_id}.jpg" \
  --json
```

### Edit with reference (image_input)

```bash
URL=$(vsb upload ./product.jpg --json | jq -r '.url')
vsb run image/nano-banana \
  --prompt "Place this mug on a sunny tropical beach with sand and ocean waves in the background. Keep the mug's pattern, colour, and reflections exactly as they are. Photoreal, natural midday daylight." \
  --image_input "[\"$URL\"]" \
  --aspect_ratio match_input_image \
  --download "./out/" \
  --json
```

### Poster with crisp typography (Pro)

```bash
vsb run image/nano-banana-pro \
  --prompt "A minimal travel poster. Composition: 9:16 vertical, full-bleed. Background: silhouette of a mountain range at dusk, indigo-to-magenta sky. Foreground typography: the word 'PATAGONIA' rendered in bold, white, condensed sans-serif font, baseline aligned at the bottom third. Subline reads 'Edge of the World' in a smaller serif italic directly underneath. Cinematic, slight film grain." \
  --aspect_ratio 9:16 \
  --output_format png \
  --download "./out/poster.png" \
  --json
```

### Multi-reference character blend (Pro shines here)

```bash
A=$(vsb upload ./pose.jpg --json | jq -r '.url')
B=$(vsb upload ./style.jpg --json | jq -r '.url')
C=$(vsb upload ./background.jpg --json | jq -r '.url')
vsb run image/nano-banana-pro \
  --prompt "Render a single image. Use Image A for the subject's pose, Image B for the art style and palette, and Image C for the background environment. Keep the subject's face and body proportions consistent with Image A. Cinematic, high-detail." \
  --image_input "[\"$A\",\"$B\",\"$C\"]" \
  --aspect_ratio match_input_image \
  --json
```

## Sources

- Google DeepMind — Gemini Image prompt guide: <https://deepmind.google/models/gemini-image/prompt-guide/>
- Google Cloud — Ultimate prompting guide for Nano Banana (2026-03): <https://cloud.google.com/blog/products/ai-machine-learning/ultimate-prompting-guide-for-nano-banana>
- Google blog — 7 tips for Nano Banana Pro: <https://blog.google/products-and-platforms/products/gemini/prompting-tips-nano-banana-pro/>
