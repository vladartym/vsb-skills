# Talking-head testimonial frame

The "creator sat their phone on a tripod and is talking to camera" frame.
Used for problem/solution ads, single-take testimonials, and any creative
that wants the subject *speaking* — even when the deliverable is a still
that will later be animated, this is the format that produces the right
first frame.

If the user wants a posed selfie or a hook shot, use `selfie-pov.md`
instead.

## Camera rules

Inverse of the selfie format. Everything is **static**.

- **Locked-off**. No pan, no zoom, no push-in, no handheld wobble.
- **Medium close-up** — chest to head. Tighter ("head and shoulders") works
  for hook shots; full-body almost never appears in talking-head UGC.
- **Eye level, dead-on**. The subject is looking *into* the camera, not down
  at it. This is the opposite of the selfie POV rule.
- **Subject centered**. The whole point is that the subject isn't going to
  move — the frame is the stage. Slightly off-center is allowed but the
  composition is intentional, not candid.
- **Background slightly blurred** with shallow depth of field — `"50mm
  lens look with shallow depth of field, background softly blurred"`.

## Gesture scripting

Under-specified hands are the single biggest failure mode in talking-head
AI. Static A-pose hands look frozen. Wildly gesticulating hands warp. The
solution: write the gestures at low resolution, like a screenwriter directs
an actor.

Working gesture vocabulary from the corpus:

- `"subtle shifts in posture as he speaks, a slight lean forward on a key
  word"`
- `"hands rest on the desk but move naturally with the rhythm of speech —
  small open-palm gestures just above the desk surface"`
- `"fingers spreading slightly to emphasise a point, hands coming together
  briefly then relaxing apart"`
- `"occasional single hand lift of a few inches then settling back down"`
- `"head nods gently on emphasis words, tilts slightly when making a point,
  small natural turns left and right as he speaks"`
- `"eyes stay on camera throughout with natural blinks, occasional brief
  look down to gather a thought then back up"`

For a still frame (not the eventual video), pick *one* gesture moment and
write it as the action slot. For example: `"one hand resting flat on the
desk, the other lifted a few inches off the desk with the palm slightly
open, mid-sentence."` Specific beats generic.

## Performance-direction parentheticals

For scripted talking-head work, write the dialogue with screenwriter-style
parentheticals. Modern image and video models interpret these correctly
because they pattern-match on the screenplay format from training. From
@CurieuxExplorer's Premier Protein script:

```
(light breath, casual tone, slight smile)
"I used to overcomplicate everything… workouts, diet, all of it."

(shakes head slightly, relaxed honesty)
"Tracking every little thing… and still feeling off."

(puts phone aside, tone softens, more grounded)
"So I stopped trying to do everything perfectly."
```

Parentheticals also work in image-only prompts to lock the micro-expression:
`"(slight smile, slightly tired eyes, mid-sentence — mouth lightly open
on a vowel)"`.

## Full filled template (still frame)

```
Ultra-realistic talking-head testimonial frame for a 9:16 vertical ad.
Shot on iPhone 15 Pro mounted on a tripod, front camera feel, not DSLR,
not studio quality. The shot is completely locked off — no movement.

[SUBJECT: e.g. A mid-30s man, light brown skin, short dark beard with
some grey at the chin, tired-but-warm eyes, soft crow's feet, natural
skin texture with visible pores across the cheeks and forehead, a small
faded scar above the left eyebrow.]

[WARDROBE: e.g. Plain navy crew-neck cotton t-shirt, slightly worn at
the collar.]

[ACTION: e.g. Sitting at his desk, leaning slightly forward with both
elbows resting near the edge of the desk. One hand flat on the desk
surface, the other lifted a few inches with the palm slightly open,
mid-sentence. Direct eye contact with the lens, small natural smile —
not posed. (relaxed honesty, conversational pacing)]

[LOCATION: e.g. Home office, mid-morning. Light wood desk, a half-full
coffee mug to his left, an out-of-focus bookshelf with a single trailing
pothos plant behind him. Plain white wall just to the right of frame.]

[LIGHTING: e.g. Soft natural window light from camera-right, warm fill
from a desk lamp camera-left. Two-source lighting feels lived-in, not
designed. No rim light, no studio lighting.]

Camera details: 50mm lens look with shallow depth of field, background
softly blurred. Slight grain, natural exposure. Medium close-up framing,
chest to head. Eye level, dead-on. No HDR, no cinematic color grading,
no rim light.

Overall vibe: feels like a real creator set their phone down to talk
about something they care about — warm, slightly tired, conversational,
unposed. 9:16 vertical.

Negatives: professional photography, studio lighting, sharp focus,
airbrushed skin, perfect composition, cinematic, 8k, masterpiece,
plastic skin, over-smoothed texture, distorted hands, extra fingers,
mannequin face, AI-perfect frozen expression, posed influencer smile,
glamour magazine aesthetic, oversaturated colors, harsh flash, rim
light, watermarks, captions, on-screen text, eyes looking off-camera.
```

## Verbatim worked examples

### Example 1 — Locked-off testimonial (@based_cub, Seedance 2.0)

Source: <https://x.com/based_cub/status/2046547012653293836>

> @image_1 @audio_1. Single continuous static shot, 9:16 vertical, 10
> seconds, camera completely locked off, no movement whatsoever. @audio_1
> is the dialogue audio. Sync lip movement and facial expression precisely
> to @audio_1. Do not generate new voice or dialogue — use @audio_1 as the
> spoken audio track exactly as uploaded. Environment: The exact scene
> from @image_1. Nothing moves except the subject. Subject: The same man
> from @image_1 […] Action: He looks directly into the camera and delivers
> the dialogue from @audio_1 with natural conversational energy. His body
> language is relaxed but engaged — subtle shifts in posture as he speaks,
> a slight lean forward on a key word, shoulders relaxed and moving
> naturally with breath. His hands rest on the desk but move naturally
> with the rhythm of speech — small open-palm gestures just above the
> desk surface, fingers spreading slightly to emphasise a point, hands
> coming together briefly then relaxing apart, occasional single hand
> lift of a few inches then settling back down. His head nods gently on
> emphasis words, tilts slightly when making a point, small natural turns
> left and right as he speaks. Eyes stay on camera throughout with
> natural blinks, occasional brief look down to gather a thought then
> back up. All movement is subtle, grounded, and human — the kind of
> natural fidgeting and gesture of someone talking earnestly to a friend
> across a desk. Camera: Completely static, locked-off, no pan, no zoom,
> no push-in, no handheld wobble. Medium shot framing identical to
> @image_1. Style: Natural fluorescent office light, no colour grading,
> no cinematic filter, ambient office room tone. Audio plays exactly as
> uploaded via @audio_1. Motion: 3. Negative prompt: no camera movement,
> no zoom, no pan, no push-in, no handheld shake, no morphing face, no
> identity drift, no changing clothing, no objects moving on desk
> unprompted, no extra people, no colour grading, no smooth skin filter,
> no background music, no lip sync drift, no mouth distortion, no
> AI-generated voice, no exaggerated gestures, no pointing at camera, no
> picking up objects.

**Why it works.** The gold standard for talking-head video. The *anti-
cinematic* lock-off is non-negotiable — `"no pan, no zoom, no push-in, no
handheld wobble"`. Hands are scripted at gesture-by-gesture resolution
because under-specified hands are the #1 failure point. The negative block
explicitly bans the failure modes (`"morphing face"`, `"identity drift"`,
`"changing clothing"`, `"objects moving on desk unprompted"`). Motion: 3
is Seedance-specific (low motion strength).

### Example 2 — Premier Protein 16s testimonial (@CurieuxExplorer, Seedance 2.0)

Source: <https://x.com/CurieuxExplorer/status/2045833962359304542>

> FORMAT: Ultra-realistic cinematic UGC video, 15–18 seconds, 24fps,
> vertical 9:16. Modern gym interior with natural daylight and soft
> ambient lighting. SUBJECT: @Character (use attached fitness avatar —
> must remain consistent across all frames, post-workout glow, slightly
> breathy, natural expression) […] DIALOGUE (~16–18 seconds): (light
> breath, casual tone, slight smile) "I used to overcomplicate
> everything… workouts, diet, all of it." (shakes head slightly, relaxed
> honesty) "Tracking every little thing… and still feeling off." (puts
> phone aside, tone softens, more grounded) "So I stopped trying to do
> everything perfectly." (glances down, picks up shaker — product enters
> naturally) "Now it's just my workout… and this." (takes a sip, relaxed
> posture) "30 grams protein, tastes like chocolate… no stress." (small
> confident smile, eye contact with camera) "And honestly… I've been way
> more consistent." PERFORMANCE NOTES: Slight post-workout breathiness.
> Natural pauses and imperfect delivery. No influencer exaggeration or
> scripted tone. Feels like talking to a friend. CAMERA AND VISUALS:
> Medium close-up framing, chest to head. Subtle handheld motion for UGC
> realism. 50mm lens look with shallow depth of field. Background softly
> blurred gym equipment. Warm neutral color grading, natural skin tones.
> No text overlays. PRODUCT PLACEMENT RULES: Product not visible in
> first half. Enters frame only at transition line "Now it's just my
> workout… and this." Held naturally, not presented to camera. Sip
> action must feel casual, not promotional.

**Why it works.** Every dialogue line carries a parenthetical performance
direction — exactly how screenwriters direct actors, and the model
interprets it correctly. The product-placement rule is also notable:
*holding the product back* until the second half is a common UGC
convention. Cinematic UGC variant — uses `"subtle handheld motion"`
rather than fully locked-off; choose locked-off (Example 1) for stronger
"real tripod" energy, handheld for "creator filming themselves." Either
works; do not mix.

### Example 3 — Walking talking-head hook (@maxxmalist, Nano Banana base)

Source: <https://x.com/maxxmalist/status/2027420767026069747>

Image stage (Nano Banana):

> Subject: Same base character from reference. Composition: Medium
> close-up selfie framing, slightly below eye level, centered. 9:16.
> Action: Self-deprecating expression, mouth slightly open mid-sentence,
> relaxed brows, direct eye contact. One hand loosely near chin. Setting:
> Tan couch, fairy lights and shelf behind. Style: Realistic iPhone
> selfie UGC, no filter. Lighting: Warm window light from left.

Paired Kling motion prompt:

> Vertical 9:16 selfie-style video in the same cozy bedroom, tan couch,
> fairy lights behind. She blinks naturally and makes a small head tilt
> like she's telling a friend something. Subtle handheld micro-jitter
> only, camera stays essentially static. [Character: warm conversational
> female voice, slightly self-deprecating]: "I actually thought my face
> was just fat… like genuinely." (short pause) "I accepted it." Audio:
> soft bedroom room tone, no music. Duration: 5s. Negative: distorted
> face, extra fingers, flicker, camera zoom/pan, watermark.

**Why it works.** Demonstrates the *image-then-video pipeline* every top
operator uses. Image stage is short and slot-clean. Video stage adds
only motion, dialogue, audio, and a tight negative list. The hook line
`"I actually thought my face was just fat… like genuinely"` is plain
conversational copy, not influencer cadence — that's the script-craft
side of UGC.

## Talking-head-specific failure modes

| Failure | Fix |
|---|---|
| Eyes drift off-camera, looking at the floor or to the side | Add `"direct eye contact with the lens"` to the action slot. Add `"eyes looking off-camera"` to negatives. For video, add `"eyes stay on camera throughout with natural blinks"`. |
| Mouth distortion mid-word | Add `"natural mouth shape, no mouth distortion"` to negatives. For video, lower motion strength (`Motion: 3` on Seedance). |
| Posed influencer smile / over-enunciated TikTok energy | Add `"(relaxed honesty, conversational pacing)"` or `"(slight smile, slightly tired eyes)"`. Add `"no influencer exaggeration or scripted tone"`. Replace `"smiling brightly"` with `"small natural smile, not posed"`. |
| Frozen A-pose hands | Script the hand at low resolution: `"one hand resting flat on the desk, the other lifted a few inches with the palm slightly open, mid-sentence"`. Don't say just `"natural hand gestures"` — name a position. |
| Handheld drift when you wanted static | Hammer the camera spec: `"camera completely locked off, no pan, no zoom, no push-in, no handheld wobble"`. Add `"tripod mounted"` if the model is misinterpreting. |
| Background looks like a generic office or gym stock | Add 2–3 specific objects: `"half-full coffee mug to his left, an out-of-focus bookshelf with a single trailing pothos plant"`, `"a single yoga mat rolled up against the wall"`. |
| Identity drifts between shots in a multi-take ad | Repeat the full subject + wardrobe description **verbatim** at every shot boundary. Add to negatives: `"face drift between shots, altered eye shape, altered freckle placement, hair change, outfit change"`. See @ViralOps_'s walking-UGC prompt in the source dossier for the canonical pattern. |
| Subject looks too "professional" / studio-lit | Re-check device token (`"shot on iPhone 15 Pro mounted on a tripod"`) and remove anything that says `"professional photography"`, `"cinematic"`, `"studio lighting"`, `"rim light"`. Two-source natural lighting (window + lamp) beats single-source. |
| Talking-head video has phantom background music | Add `"no music, no background score, ambient room tone only"`. Models hallucinate music tracks if not told otherwise. |

## Tips

- For a still frame (no animation yet), generate at 9:16 in Nano Banana Pro
  with the template above, then use it as the reference image for a video
  pass in Kling, Seedance, or Veo.
- For pure image testimonials, the failure-mode list above still applies —
  treat the still as a single freeze-frame from the testimonial video.
- Use the same wardrobe and lighting setup across all assets in an ad set.
  Even small wardrobe drift (collar color, jewelry) ruins the illusion.
