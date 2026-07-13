---
name: vsb-download
description: >
  Download a video from TikTok, Instagram, YouTube (any yt-dlp-supported
  site) via `vsb download`, then actually understand it — extract frame
  grids with ffmpeg, read them as images, and reconstruct the dialogue from
  burned-in captions. Trigger when the user shares a video URL and asks
  "what is this video about", "watch this", "summarize this video",
  "download this tiktok/reel/short", or wants a local copy of a social
  video. Metadata-only lookups use `--info` (no download, no bytes).
---

# Download + understand a video with vsb

`vsb download` wraps a pinned, SHA-256-verified `yt-dlp` (bootstrapped to
`~/.vsb/tools/` on first use — no global install needed). It downloads the
video file and prints structured metadata. It does **not** interpret the
content — that's the frame-grid pipeline below.

## Command surface

```bash
vsb download <url> --json                 # download to cwd, default name "<title> [<id>].<ext>"
vsb download <url> --info --json          # metadata only, nothing downloaded
vsb download <url> -o ./clips/ --json     # trailing "/" = directory
vsb download <url> -o ./clip.mp4 --json   # exact filename (yt-dlp template syntax allowed)
vsb download <url> --cookies chrome --json  # login-walled posts (Instagram) — reads local browser cookies
```

- Works on any yt-dlp-supported site: TikTok, Instagram, YouTube, X, and hundreds more.
- `--cookies` takes a browser name (`chrome`, `safari`, `firefox`). Needed for
  Instagram and other login-walled posts. On macOS the first use may prompt
  for keychain access — that prompt goes to the user's screen, warn them.
- Always `--json` when an agent reads the output.

## Step 1 — metadata first, always

`--info` is free and instant. Run it before deciding to download at all:

```bash
vsb download "$URL" --info --json
```

```json
{
  "status": "info",
  "id": "7639535671257992468",
  "title": "What do you guys think of the new studio? #djcara ...",
  "description": "full caption text with #hashtags",
  "uploader": "djcara.com",
  "duration_seconds": 9,
  "width": 576, "height": 1036,
  "source": "TikTok",
  "upload_date": "20260514",
  "view_count": 824, "like_count": 18
}
```

Often the caption + duration already answers the user's question. Only
download when you need to see the actual content. `upload_date` is
`YYYYMMDD`. `duration_seconds` drives the sampling plan below.

## Step 2 — download to scratchpad

Never download into the user's project. Use the session scratchpad (or ask
where, if the user wants to keep the file):

```bash
vsb download "$URL" -o "$SCRATCHPAD/clip.mp4" --json
```

## Step 3 — frame grids (see the whole video in one Read)

Tile evenly-sampled frames into a single image, then read that image. One
grid of 9 frames covers a short video end-to-end and costs one image read.

```bash
# 3x3 grid, one frame per second — right for clips ≤ 12s
ffmpeg -y -loglevel error -i clip.mp4 \
  -vf "fps=1,scale=288:-1,tile=3x3" grid.jpg
```

Pick sampling from `duration_seconds` so the whole video fits in 1–3 grids
(~9 frames per grid):

| Duration | Filter | Grids |
|----------|--------|-------|
| ≤ 12s | `fps=1,scale=288:-1,tile=3x3` | 1 |
| 12–60s | `fps=9/DUR,scale=288:-1,tile=3x3` (evenly spaced) | 1 |
| 1–5 min | `fps=18/DUR,scale=288:-1,tile=3x3` → `grid_%d.jpg` | 2 |
| 5 min+ | scene changes: `select='gt(scene,0.3)',scale=288:-1,tile=4x4` + `-vsync vfr` | 1–2 |

Compute the fraction in shell (ffmpeg accepts `fps=9/47` literally):

```bash
DUR=$(vsb download "$URL" --info --json | jq -r '.duration_seconds')
ffmpeg -y -loglevel error -i clip.mp4 -vf "fps=9/$DUR,scale=288:-1,tile=3x3" grid.jpg
```

Multi-grid output: name the output `grid_%d.jpg` and read each grid in order.

Tile-size rules:
- `scale=288:-1` (288px wide per tile) reads fine for vertical 9:16 video,
  captions included.
- Landscape/YouTube: use `scale=-1:288` so height is fixed instead.
- Captions too small to read? Re-run just the interesting seconds bigger:
  `-ss 4 -t 3 ... -vf "fps=2,scale=480:-1,tile=2x2"`.

## Step 4 — dialogue via burned-in captions

TikTok / Reels / Shorts almost always burn auto-captions into the frames.
Reading the grids in order reconstructs the spoken script for free — no
transcription step. Quote it in the summary.

No burned-in captions and the audio matters? There is currently **no
transcription path in the CLI** (`vsb` has no audio-understanding model).
Say so honestly — describe the visuals, don't guess the dialogue — and flag
the gap with `vsb feedback` (ask the user first).

## Step 5 — report, then clean up

- Summarize: what happens on screen, the spoken script (from captions), the
  poster's caption/hashtags, stats (views/likes, upload date).
- The scratchpad copy dies with the session. If the user might want the
  file, ask once: keep it? where? Move it — don't re-download.

## Traps

- `--info` still hits the network (yt-dlp metadata fetch) — it just skips
  the video bytes.
- Default output template (no `-o`) writes `<title> [<id>].<ext>` into the
  **current directory** — titles contain spaces and `#hashtags`; always
  quote the resulting path, better yet always pass `-o`.
- Instagram without `--cookies` fails with a login error — that's the
  expected signal to retry with `--cookies chrome`.
- ffmpeg required for the understanding pipeline (`command -v ffmpeg`); the
  download itself needs only `vsb`.
- Age-gated / region-locked / private videos fail inside yt-dlp with its
  error passed through — quote it, don't retry blindly.
