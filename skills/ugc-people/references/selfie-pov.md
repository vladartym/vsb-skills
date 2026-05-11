# Selfie / phone-camera POV

The hook-shot format. Used when the creative needs to feel like a real
creator grabbed their phone and caught a moment — for the first 1–2 seconds
of a TikTok / Reels / Shorts ad, or as a static feed-ad creative pretending
to be UGC.

If the user wants a posed studio shot or a polished product photo, this is
the wrong format. Use the broader `image` skill instead.

## Camera rules

These are non-negotiable for the format. Drop any of them and the shot stops
reading as "real phone selfie."

- **Handheld**, with micro-jitter. Never `tripod`, never `locked-off`.
- **Eye level slightly above the subject**, camera angled down. This mimics
  the natural angle when you hold your phone at arm's length. Below-eye-level
  works for confident hook framing but is the exception.
- **Headroom minimal** — hair lightly clipped at the top of the frame, head
  filling the upper third. Cinematic centered framing reads as professional,
  not phone.
- **Off-center framing is good.** `"framing feels candid and handheld,
  slightly imperfect and not perfectly centered"`.
- **Lens distortion from the front camera** is a positive cue, not a bug:
  `"slight lens distortion from front camera"`.

## Full filled template

Copy this, swap the bracketed slots for your subject and scene, run it. Every
slot is filled with a working fragment from the verbatim corpus.

```
Ultra-realistic casual indoor selfie taken on a smartphone. Shot on
iPhone 15 Pro, front camera feel, not DSLR, not studio quality. Slight
motion softness, mild grain, natural exposure, no over-sharpening or
AI over-detailing.

[SUBJECT: e.g. A young woman, early-to-mid 20s, mixed-heritage, natural
warm skin with visible pores across the T-zone and faint freckles across
the bridge of the nose, peach fuzz catching the light along her jawline,
messy low bun with baby hairs visible at the hairline.]

[WARDROBE: e.g. Short-sleeve beige ribbed scoop-neck top, small silver
hoop earrings, a delicate gold chain necklace.]

[ACTION: e.g. Holding the phone in her right hand at face level, mid-laugh
with her head tilted slightly back, free hand loosely near her chin. Mouth
slightly open mid-sentence, eyes softly toward the lens, relaxed brows.]

[LOCATION: e.g. Modern kitchen interior with floor-to-ceiling flat white
cabinetry, polished concrete flooring, a wooden cutting board with a half-
peeled orange on the counter behind her.]

[LIGHTING: e.g. Soft natural morning light from a window camera-left, warm
fill from a recessed ceiling lamp above. No artificial flash, no studio
lighting, no dramatic highlights.]

Camera details: slight motion blur, natural noise typical of a phone
camera, subtle lens distortion from front camera, no HDR or cinematic
color grading. Framing feels candid and handheld, slightly imperfect and
not perfectly centered, head lightly clipped at the top of the frame.

Overall vibe: candid, unposed, natural lifestyle selfie — looks like a
quick real moment, not staged or professionally shot. 9:16 vertical.

Negatives: professional photography, studio lighting, sharp focus,
airbrushed skin, perfect composition, cinematic, 8k, masterpiece,
plastic skin, over-smoothed texture, distorted hands, extra fingers,
warped iPhone shape, harsh flash, harsh studio lighting, glamour magazine
aesthetic, oversaturated colors, mannequin face, AI-perfect frozen
expression, posed influencer smile, watermarks, captions, on-screen text.
```

## Verbatim worked examples

Real prompts from operators, copied as-posted. Use as reference for slot
density and phrasing — not as drop-in scripts.

### Example 1 — Casual indoor selfie (@sumiturkude007, GPT-image 2)

Source: <https://x.com/sumiturkude007/status/2050802017162174912>

> Ultra-realistic casual indoor selfie taken on a smartphone (iPhone front
> camera feel, not DSLR, not studio quality). Slight motion softness, mild
> grain, natural exposure, no over-sharpening or AI over-detailing. A young
> woman sitting on a wooden floor in a modern living room, leaning slightly
> back with one arm supporting her body. She is holding a smartphone in one
> hand at face level, looking at the screen with a neutral, slightly serious
> expression. The framing feels candid and handheld, slightly imperfect and
> not perfectly centered. She has long, straight blonde hair, naturally
> styled and falling over her shoulders. Her face has natural skin texture
> with minimal or no makeup. Outfit: White fitted long-sleeve crop top,
> Light blue leggings, White socks, Cream-colored leg warmers scrunched
> around her calves. Body posture is relaxed, legs bent casually in front of
> her. Lighting is soft natural indoor light, slightly dim with gentle
> shadows and mild contrast. No artificial lighting or dramatic highlights.
> Background: Modern living room setting with a gray sofa behind her,
> Minimalist wall art frames visible on the wall, Clean but lived-in
> environment, Wooden floor visible in foreground. Camera details: Slight
> blur and natural noise typical of a phone camera, Subtle lens distortion
> from front camera, No HDR or cinematic color grading. Overall vibe:
> candid, unposed, natural lifestyle selfie—looks like a quick real moment,
> not staged or professionally shot.

**Why it works.** Clean slot order — device → subject pose → outfit →
lighting → environment → camera artifacts → vibe sentence. Negatives are
baked into positive phrasing (`"not DSLR, not studio quality"`,
`"no over-sharpening"`) rather than a separate block. The single line
`"iPhone front camera feel, not DSLR, not studio quality"` is the dominant
authenticity lever.

### Example 2 — Mirror selfie (@MaxfusionAI, Nano Banana 2 vs GPT-image 2)

Source: <https://x.com/MaxfusionAI/status/2052039424112804186>

> Ultra-realistic iPhone selfie photo of a young woman taking a mirror
> selfie in her bedroom. A realistic handheld phone selfie taken in front
> of a full-length or wall-mounted mirror in a cozy lived-in bedroom. The
> phone is held up at chest-to-shoulder height, partially covering one side
> of her face, with the bright camera flash off and natural ambient light
> illuminating the scene. The phone is a matte white iPhone with the
> recognizable triple-camera lens module clearly visible on the back, held
> in her right hand close to the mirror. She is angled sideways in
> three-quarter profile, looking softly toward her own reflection with a
> half-lidded pout — bored, pretty, slightly dramatic expression.
> Hyper-realistic skin texture with visible pores across the T-zone, faint
> freckles across the bridge of the nose, peach fuzz catching the light
> along her jawline. No blemishes or acne — clear healthy skin but textured
> and real, not airbrushed. […] realistic mirror reflection physics —
> left-right inversion of any text or asymmetric details — the phone
> covering part of her face is sharp while the room behind in the
> reflection has gentle natural depth blur. […] Natural skin texture, no
> beauty filter, no smoothing, no AI-perfect plastic skin. […] Negative
> elements to avoid: overly smoothed skin, plastic airbrushed texture,
> flawless porcelain skin, AI-perfect frozen expression, mannequin face,
> harsh studio lighting, glamour magazine aesthetic, oversaturated colors,
> harsh flash, distorted hands, extra fingers, warped iPhone shape …

**Why it works.** Mirror-selfie variant of the format. Names the phone
hardware (`"matte white iPhone with the recognizable triple-camera lens
module"`). Adds an explicit *physics* line — `"realistic mirror reflection
physics — left-right inversion of any text or asymmetric details"` — without
which mirror selfies usually break. Pairs every `"no plastic skin"` line
with *positive* texture cues (pores, peach fuzz). Dense environment
specificity (sage-green linen bedding, woven basket, pothos plant in the
full version) prevents stock-apartment defaults.

### Example 3 — Genuine laugh portrait (@heisturnx, Nano Banana Pro)

Source: <https://x.com/heisturnx/status/2051753793025646991>

> Ultra-realistic iPhone selfie, African female, natural dark skin, genuine
> laughing moment (head tilted back), laugh lines visible around eyes,
> pores visible, messy curly hair, warm golden hour light from window.
> Outfit: simple white tank top. Pose: mid-laugh, phone held naturally,
> natural movement captured. Lighting: soft golden hour from side, warm
> glow, natural shadows, laugh lines visible. Camera: iPhone 13, motion
> blur from laugh, warm color, skin texture preserved, no smoothing.
> Details: laugh lines, open mouth genuine smile, teeth visible, eyelashes
> catch light, facial hair visible, natural flush on cheeks, unguarded
> moment. Mood: 'caught laughing at something real'. NO filters, NO beauty
> mode, NO posed smile.

**Why it works.** Short, dense, aggressive on anti-AI cues. The mood line
`"caught laughing at something real"` is one of the most reusable single
fragments in the corpus — it forces a real micro-expression instead of a
posed open-mouth smile, which is one of the most common AI tells. Also
nails the iPhone-token pattern with a specific model (`iPhone 13`).

## Selfie-specific failure modes

| Failure | Fix |
|---|---|
| Subject perfectly centered, looks like a posed studio shot | Add `"framing feels candid and handheld, slightly imperfect and not perfectly centered"` plus `"head lightly clipped at the top of the frame"` |
| Symmetric face, mannequin look | Add `"slight three-quarter profile"` or `"head tilted slightly"` to the action slot |
| Posed influencer smile, perfect teeth | Replace `"smiling at camera"` with `"mid-laugh"`, `"mouth slightly open mid-sentence"`, or `"small natural smile, not posed"`. Add `"caught laughing at something real"` as a mood line. |
| Glossy plastic skin | Add positive texture cues inline: `"visible pores across the T-zone, faint freckles, peach fuzz along the jawline, subsurface scattering"`. Don't rely on negatives alone. |
| Warped hand holding the phone, melted fingers | Specify the hand's job explicitly: `"holding the phone in her right hand at face level, free hand loosely near her chin"`. Add `"distorted hands, extra fingers, warped iPhone shape"` to the negatives. |
| Mirror selfie where the reflection breaks | Add the physics line: `"realistic mirror reflection physics — left-right inversion of any text or asymmetric details"`. Name the phone hardware explicitly so the model has something solid to reflect. |
| Image looks like a magazine ad, not a phone | Re-check that you have the device token (`"shot on iPhone …, front camera feel, not DSLR, not studio quality"`) and that you have *not* said `"professional photography"` or `"studio lighting"` anywhere in the positive prompt. |
| Generic apartment / kitchen background | Add 2–3 specific objects to the location slot (`"a half-peeled orange on the counter"`, `"sage-green linen bedding"`, `"woven basket of laundry"`) — generic descriptions get generic backgrounds. |

## Tips

- Generate at native 9:16 (`--aspect_ratio 9:16`) rather than cropping a 1:1.
  The model frames differently for portrait orientation.
- For mirror selfies, name the phone hardware *in* the prompt — the model
  will draw it more reliably than if you let it imagine "a phone".
- For multi-shot ad sets, write the subject + wardrobe slots out fully in
  *every* prompt verbatim — don't try to shorten by referencing "the same
  person." See `talking-head.md` for the consistency rules.
