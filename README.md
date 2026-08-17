# Hush — White Noise

A calm, single-purpose white-noise player built as a **PWA**, tuned for iPhone. Every
sound is **synthesized in the browser** with the Web Audio API — there are no audio
files to download, so the whole app is a few kilobytes and works completely **offline**.

## Sounds

White · Pink · Brown · Rain · Ocean · Fan — each freshly synthesized as a seamless
**5-minute** loop (equal-power crossfade at the loop boundary, so there's no click or
thump). No audio clip is shipped and nothing is seeded, so no two sessions are the same
waveform.

### Why 5 minutes — the "no audible pattern" design

The thing that makes most white-noise apps sound *looped* is a short clip (often
5–15 seconds) repeating. To keep sound playing while your **phone is locked**, iOS forces
a natively-looped `<audio>` buffer (live Web Audio is suspended on lock), and a loop
repeats by definition — so the only real lever is **length**. At 5 minutes the ear never
latches onto the repeat. Filters are kept low-Q and the ocean's wave period is long, so
there are no recurring tonal/resonant artifacts either. Want it even longer? It's a single
constant — `LOOP_SECONDS` in `index.html` (costs ~5.3 MB per extra minute).

## Features

- **Sleep timer** (15 / 30 / 45 / 60 / 90 min) with a gentle 20-second fade-out.
- **Volume** that works on iOS (see below).
- **Lock-screen playback** and Control Center metadata via the MediaSession API.
- Remembers your last sound, volume, and timer.
- Installs to the Home Screen; runs full-screen and offline.

## Install on iPhone

Open the page in Safari, then **Share → Add to Home Screen**. Launch it from the icon
for a full-screen, offline app.

## Why it's built the way it is (iOS gotchas)

- **Playback survives screen-lock.** Sound plays through an `<audio loop>` element, not
  live Web Audio, because iOS suspends `AudioContext` when the screen locks — fatal for a
  sleep app. Each sound is pre-rendered to a looping WAV (via `OfflineAudioContext` for
  filtering) and played as media.
- **Volume actually changes.** iOS ignores `audio.volume`, so volume is baked into the
  rendered WAV and re-encoded on change. A noise restart is imperceptible, and it keeps
  working while locked.
- **No dependencies, no build.** Plain HTML/CSS/JS in `index.html`. Just open it, or
  serve the folder statically.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole app — UI, synthesis, playback |
| `manifest.webmanifest` | PWA metadata |
| `sw.js` | Service worker (offline shell cache) |
| `icon.svg` | App icon |

## Run locally

```sh
python3 -m http.server 8000   # then open http://localhost:8000
```

A service worker needs `http(s)` (or `localhost`), not `file://`, to register — but the
app itself works fine opened directly as a file too.
