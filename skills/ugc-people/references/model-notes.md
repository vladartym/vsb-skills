# Model notes for UGC people

Per-model quirks for prompting people in UGC ad creative. Drawn from
operator reviews and side-by-side comparisons across 2026. Verify the live
catalog before relying on any slug:

```bash
vsb models --modality image --json | jq '.models[] | {slug, category, owner}'
```

## Nano Banana / Nano Banana Pro (Gemini 3.x Image)

**Slugs:** `image/nano-banana`, `image/nano-banana-2`, `image/nano-banana-pro`.

**Strengths.**
- Best-in-class **prompt adherence** for complex compositional instructions
  ("subject left, product right, headline top, CTA bottom"). Operators
  report usable-text rate climbed from ~30% on older models to >90% on
  Nano Banana Pro for ad assets that need legible copy.
- Best-in-class **text rendering** — product labels, headlines, button
  copy. Reach for this when the deliverable has on-image text.
- **Multi-shot character consistency** is the strongest of the in-scope
  models. Repeat the subject + wardrobe verbatim and pass the prior image
  as a reference; identity holds across 3–5 shots reliably.
- Edit mode (with `image_input` array) preserves both face *and* product
  label from references — useful for product-in-hand UGC.

**Weaknesses.**
- The baseline aesthetic is *polished AI* — clean, slightly smooth,
  slightly cinematic. Without explicit anti-plastic cues the output reads
  as "high-quality AI image", not as "real iPhone photo." Always pair with
  the texture vocabulary from `SKILL.md` § "Anti-plastic vocabulary".
- Slow-to-warm on aggressive negative-energy prompts. The `@mrJackLevin`
  "failed candid shot" style works better on GPT-image 2 than on Nano
  Banana — Nano Banana tends to clean up the "mistake" you asked for.

**When to reach for it.**
- Multi-shot ad sets where character consistency matters.
- Any creative with on-image text or a readable product label.
- Product-in-hand UGC where the product brand must be legible.
- When the next step is an edit-mode pass on a base image.

## GPT-image 2 (OpenAI Image 2)

**Slug:** `image/gpt-image-2`.

**Strengths.**
- **Current operator favourite for raw UGC realism with people.**
  @heyrobinai (May 2026): *"ChatGPT 2.0 is insanely good at generating
  ultra-realistic UGC … so much better than the previous model at
  creating photorealistic humans."*
- Accepts negative-energy / failure-mode prompts well — `"failed candid
  shot"`, `"completely mundane"`, `"accidental selfie"`, `"weird angle
  and a cluttered frame"`. This is the @mrJackLevin / @Adonnorx
  prompting style that produced the viral "AI takes a selfie" thread.
- Renders **skin texture and imperfection cues** more readily than Nano
  Banana — visible pores, peach fuzz, slight overexposure all land with
  fewer reminders.

**Weaknesses.**
- **Text rendering is weaker** than Nano Banana Pro. Can still mangle
  product labels, signage, headlines. Reach for Nano Banana Pro instead
  when on-image text matters.
- **Complex multi-element compositions** ("subject + product + on-screen
  UI mock + headline") are less reliable than Nano Banana Pro. GPT-image
  2 prefers a single dominant subject.
- Identity consistency across multiple generations is weaker — for
  multi-shot work, use Nano Banana Pro or pair GPT-image 2 with a
  reference image lock.

**When to reach for it.**
- Single-shot UGC people images where realism is the only thing that
  matters.
- "Ugly / native ad" energy where you want the image to look slightly
  haphazard, not polished.
- Mundane-snapshot prompts (the "accidental selfie" pattern).

## Flux 1.1 Pro / Flux 2 (Black Forest Labs)

**Slug:** look for `image/flux-1.1-pro` or similar under `vsb models`.
**May not be in the current Visual Sandbox catalog** — verify with `vsb
models --modality image --json` before assuming availability. If it's not
there, prefer GPT-image 2.

**Strengths.**
- The **"UGC forgery" model**, per @asadrhmn (LTV.ai, January 2026 review,
  10M+ images generated commercially): *"Flux produces that 'accidentally
  good photo' aesthetic. the slightly imperfect lighting. the texture.
  consumers have developed pattern recognition for AI images … they scroll
  past it."*
- One DTC apparel customer reported a 23% lift in Meta conversions after
  switching AI lifestyle photos from a "polished AI" model to Flux.
- **Short prompts work best.** @mike_branc's one-line prompt: *"A tik tok
  style photo of a girl in her 20s sitting in her car from a selfie angle
  with blonde hair and brown eyes"* — produced usable UGC. Over-specifying
  pushes Flux back toward the polished basin.

**Weaknesses.**
- **Inconsistent text rendering.** Avoid for anything with on-image copy.
- **Multi-shot consistency** is weaker than Nano Banana Pro. Use as a
  one-off generator, not as a multi-take character.
- Reads instructions more loosely — drift from the prompt is more common.

**When to reach for it.**
- Single, gritty, realistic UGC lifestyle photo where the brief is "make
  it look like a real Instagram post, not an ad."
- A/B testing a polished-AI render against a Flux render to see which
  converts on Meta.

## Midjourney v7 (v8 preview)

**Slug:** typically not exposed via vsb (Midjourney has no public API).
Pair with a base-character workflow if needed.

**Strengths.**
- **Aesthetic ceiling.** @nickfloats's side-by-side of a melancholic
  medieval scene showed v8-preview capturing *"her eyebrows drawn with
  quiet distress"* in a way Nano Banana could not match. Strongest for
  emotional nuance.
- Excellent **base-character generation** — use `--sref`, `--cref`, and
  the personalization profile to lock an influencer-style face, then
  send the result to Nano Banana / Flux / GPT-image 2 for the actual UGC
  scenes.

**Weaknesses.**
- The default aesthetic is **too artistic for direct UGC**. Midjourney
  composes like a photographer rather than like a phone. Outputs read as
  "professional fashion photo" by default, even with anti-plastic cues.
- **Walled garden**. Hard to integrate programmatically without scraping
  Discord.

**When to reach for it.**
- *Not* for direct UGC generation.
- As a base-character generator only: get the face you want, then use that
  image as a reference in another model for the actual UGC scenes.
- For lifestyle-but-aspirational shots where some art-direction is
  acceptable.

## Higgsfield Soul / Soul 2 (reference, not in vsb)

**Not a vsb model.** Included here because operators reference Higgsfield as
the current UX target for "UGC people" workflows.

**Why it matters for this skill.**
- Higgsfield is the **preset-driven UX** competitor. Operators (per
  @HeyAbhishek, @_zegalone, @Shabbir3320) don't write prompts — they pick
  a preset, upload a base image, click generate. That is the UX target
  Visual Sandbox is building toward (see the `presets` skill).
- Its image quality for people is at parity with or above Nano Banana Pro
  for the UGC use case specifically — it's purpose-built for "hyper-real
  humans, expressive faces, consistent character identity."

**How to use this in vsb.**
- Don't try to reproduce Higgsfield directly. Use the `presets` skill to
  save proven UGC prompts as reusable presets (e.g. `ugc-selfie-pov`,
  `ugc-talking-head`), each pre-configured with the right model,
  aspect ratio, and slot template.
- A preset library is the closest equivalent to Higgsfield's UX inside
  Visual Sandbox.

## Picking a model — one-line decision rule

1. Need on-image text or multi-shot consistency? → **Nano Banana Pro**
2. Single shot, want raw realism, no text? → **GPT-image 2**
3. Single shot, want "accidental Instagram post" gritty look? → **Flux**
   (if available; otherwise GPT-image 2)
4. Need a custom base character to lock identity across many shots? →
   **Midjourney** to generate the base, then bring it back to Nano Banana
   Pro as a reference image.

## Identity consistency across all models

No single in-scope model holds character identity perfectly across many
shots from prompt alone. The working pattern, used by every operator
running multi-shot AI UGC ads in 2026:

1. **Generate a base reference image** with the most flattering model for
   the face you want (often Midjourney or Higgsfield Soul; Nano Banana Pro
   if staying in vsb).
2. **Feed that reference back in** for every subsequent generation
   (`image_input` array on Nano Banana, `--cref` on Midjourney,
   `@reference` token on Seedance / Kling).
3. **Repeat the character description verbatim** at every shot boundary.
   Don't shorten it to "the same person" — write out the full subject +
   wardrobe each time.
4. **List drift in the negatives:** `"face drift between shots, altered
   eye shape, altered freckle placement, hair change, outfit change"`.

Source for this pattern: @xinyuan_yin's lock-down prompt
(<https://x.com/xinyuan_yin/status/2043086696401768646>) and @ViralOps_'s
multi-shot UGC walking ad (<https://x.com/ViralOps_/status/2046116261294407810>).
