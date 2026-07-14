---
name: vsb-subtitles
description: >
  Add TikTok/CapCut-style word-by-word captions to any video with
  `vsb subtitles` — transcribes via Visual Sandbox's `audio/scribe`
  (ElevenLabs Scribe, word-level timestamps), builds kinetic .ass
  subtitles (ALL-CAPS bold, per-word pop-in, clause-by-clause reveal),
  and burns them in with local ffmpeg. Trigger when the user says
  "add subtitles", "add captions", "caption this video", "burn subs",
  or wants a transcript of a video with timestamps. Accepts a local
  file or a TikTok/Instagram/YouTube URL.
---

# Subtitle a video with vsb

One command does the whole pipeline: download (if URL) → probe duration →
extract audio → transcribe via `audio/scribe` → build kinetic captions →
burn with ffmpeg. Requires local `ffmpeg` (the transcription itself is the
only paid step).

## Command surface

```bash
vsb subtitles ./clip.mp4 --json                 # local file → ./clip-subtitled.mp4
vsb subtitles "https://www.tiktok.com/@user/video/123" --json
vsb subtitles ./clip.mp4 -o ./out.mp4 --language en --json
vsb subtitles ./clip.mp4 --font "Montserrat ExtraBold" --font-size 42 --json
vsb subtitles ./clip.mp4 --no-uppercase --json  # keep original casing
```

- Output lands next to the input as `<name>-subtitled.mp4` unless `-o` is set.
- The full transcript (word timestamps included) is saved alongside as
  `<output>.transcript.json` — use it for summaries, translations, or
  re-styling without paying for a second transcription.
- `--language` takes an ISO-639-1 code; omit to auto-detect.
- Always `--json` when an agent reads the output.

## Fix mishears, re-burn free

`--transcript <file>` reuses a saved transcript instead of transcribing —
no API call, no cost. The workflow for correcting ASR mistakes (brand
names, homophones like "paid"/"pay"):

```bash
vsb subtitles ./clip.mp4 --json                          # first pass, pays ~1¢
# edit clip-subtitled.transcript.json: fix the "text" of wrong words,
# delete hallucinated ones — KEEP the start/end timestamps
vsb subtitles ./clip.mp4 --transcript ./fixed.json --json  # re-burn, free
```

Same flag also covers restyling passes (`--font-size`, `--no-uppercase`)
without paying twice.

## Cost

Transcription is billed per hour of audio (`audio/scribe`), so a typical
short-form clip costs about a cent. `vsb pricing audio/scribe` shows the
current rate; the JSON output reports the exact `cost` charged.

## Caption style

Modeled on TikTok auto-captions: ALL-CAPS bold sans, white with a soft dark
shadow, centered in the lower third. Words appear one at a time synced to
speech — each new word pops in slightly oversized and translucent, then
settles (~120ms). Words accumulate until the clause ends (punctuation, a
speech pause > 0.8s, or the two-line budget), then the block clears and the
next clause starts.

`--font` must name an installed font; libass silently falls back to a
default sans when it's missing, so captions never fail on fonts.

## Transcribe without burning

Need only the transcript (subtitles for something else, a summary, search)?
Call the model directly and skip the render:

```bash
vsb run audio/scribe --audio ./clip.mp3 --duration_seconds 54 --json
```

`duration_seconds` is required — it sets the exact price up front. Probe it
first: `ffprobe -v error -show_entries format=duration -of csv=p=0 file`.
The result URL is a JSON file with `text` plus per-word `start`/`end`
timestamps (and `speaker_id` per word with `--diarize true`).

## Traps

- The input needs an audible speech track; music-only videos return few or
  no words and the command errors with "No speech detected".
- Videos that already have captions burned in get a second layer — there is
  no way to remove baked-in captions. Prefer a clean source export.
- `ffmpeg`/`ffprobe` must be on PATH (`brew install ffmpeg`); only the
  yt-dlp download path is auto-bootstrapped.
