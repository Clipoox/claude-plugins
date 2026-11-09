---
name: clipoox-video-edit
description: Edit videos like a professional editor from an AI coding agent (Claude Code, Codex, Cursor…). Use when the user asks to cut, trim, tighten, caption, reframe (9:16 / 1:1 / 16:9), add music, fix loudness, colour-match or export a video, or to turn raw footage (talking head, interview, tutorial, product, travel) into a finished short or long video. Works with plain ffmpeg on the user's computer, and with the Clipoox Timeline (clipoox.com/mcp or the `clipoox` CLI) when it is connected.
---

# Video editing (Clipoox skill)

You are the editor. The user brings footage and a goal; you plan the edit, show the plan, cut, check your own
work, and hand back a finished file. Reply in the user's language (Vietnamese or English).

## Golden rules

1. **Never touch the sources.** Read from the user's folder, write everything to `edit/` next to it
   (`edit/segments/`, `edit/checks/`, `edit/final.mp4`). Keep a short log in `edit/project.md`
   (brief, decisions, commands that worked) so the next session can continue.
2. **Plan before cutting.** Show an edit decision list (EDL) and wait for the user's OK before rendering anything
   longer than a test.
3. **Ask before spending money.** Anything that calls a paid service (AI video/image/voice/music, paid
   transcription, Clipoox tools marked ★ / "spends credits") needs a price first (`dry_run` where available) and the
   user's yes. Free local tools (ffmpeg, whisper on the user's machine) need no confirmation.
4. **Check before you claim.** A render is done only after the checks in step 7 pass. Say what you checked.

## Workflow

### 1. Inventory
For every source: duration, resolution, fps, rotation, audio channels.

```bash
ffprobe -v error -show_entries format=duration:stream=index,codec_type,codec_name,width,height,r_frame_rate,channels \
  -of json "input.mp4"
```

Mixed fps or resolution → normalise every segment to one target (step 4). Phone footage may carry a rotation tag:
ffmpeg applies it automatically when re-encoding.

### 2. Brief (ask once, only what is missing)
- Platform and shape: TikTok/Reels/Shorts **9:16 1080×1920**, YouTube **16:9 1920×1080**, feed **1:1 1080×1080**.
- Target length, tone (energetic / calm / cinematic), must-keep moments, call to action.
- Captions on or off; brand font and colour; music (the user's own file, or none).

### 3. Understand the footage
- **What is said:** a word-level transcript. Local and free: `whisper-ctranslate2` (faster-whisper; install with
  `/clipoox-video:setup`) or `whisper` — same options:
  `whisper-ctranslate2 input.mp4 --model small --language vi --output_format srt --output_dir edit/`
  (`--model large-v3` for the best Vietnamese). On Clipoox: `transcribe`
  (small paid fee, price first) then `get_transcript`.
- **Where the pauses are:**
  `ffmpeg -i input.mp4 -af silencedetect=noise=-35dB:d=0.4 -f null - 2> edit/checks/silence.txt`
- **Where the shots change:** `scenedetect -i input.mp4 detect-adaptive list-scenes` (PySceneDetect) or
  `ffmpeg -i input.mp4 -vf "select='gt(scene,0.3)',showinfo" -an -f null - 2>&1 | grep pts_time`
- Long talking footage: `auto-editor input.mp4 --margin 0.2sec` gives a fast first pass that removes dead air, or
  `--export resolve` / `--export premiere` hands the cut to a desktop editor.

### 4. Edit decision list → rough cut
Write the EDL as a table in `edit/project.md`: `#, source, in, out, why (hook / point / proof / CTA)`.
Editorial rules that make a cut feel professional:
- **Hook in the first 1–3 s:** the strongest line, result or visual first; context later.
- **Something changes every 2–5 s:** a cut, a punch-in, a caption beat, a B-roll insert or a graphic.
- Remove filler words, false starts and repeated takes; keep the *last* good take of a line.
- Cut on action or on the beat; let a sentence finish before the cut (≥ 0.15 s of air).
- J/L cuts for interviews: the next speaker's audio may start 0.2–0.5 s before their picture.
- Talking head without B-roll: alternate 100 % and ~115 % framing (punch-in) on cuts so jump cuts look intended.

Render each segment with the same settings, with a 30 ms audio fade at both ends so no cut clicks:

```bash
# segment = in 3.20 s, length 5.50 s → fade out starts at 5.47
ffmpeg -y -ss 3.20 -i input.mp4 -t 5.50 \
  -vf "scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:(ow-iw)/2:(oh-ih)/2,fps=30,format=yuv420p" \
  -af "afade=t=in:d=0.03,afade=t=out:st=5.47:d=0.03,aresample=48000" \
  -c:v libx264 -preset medium -crf 18 -c:a aac -b:a 192k -ac 2 edit/segments/s01.mp4
```

Join them (all segments share codec, size, fps and audio format, so no re-encode is needed):

```bash
(cd edit/segments && printf "file '%s'\n" s*.mp4 > list.txt)   # paths in the list are relative to the list
ffmpeg -y -f concat -safe 0 -i edit/segments/list.txt -c copy edit/rough.mp4
```

More recipes (reframe 9:16, punch-in, speed ramp, music ducking, captions style, colour, transitions):
[references/ffmpeg-recipes.md](references/ffmpeg-recipes.md).

### 5. Polish
- **Captions:** 1–3 words per line for shorts, ≤ 42 characters per line for long videos; keep inside the
  safe area (bottom 20 % of a 9:16 frame is covered by app buttons). Vietnamese needs a font with full diacritics
  (Be Vietnam Pro, Inter, Roboto, Montserrat) and enough line height so marks are not clipped.
- **Audio:** voice first. Music 18–25 dB under the voice, ducked while someone speaks. Final loudness
  **−14 LUFS integrated, true peak ≤ −1.5 dBTP** for social; −16 LUFS for podcasts.
- **Colour:** match shots to the hero shot before adding a look; skin tones first. Small moves only.

### 6. Export
```bash
ffmpeg -y -i edit/rough.mp4 -c:v libx264 -preset slow -crf 20 -pix_fmt yuv420p -movflags +faststart \
  -af "loudnorm=I=-14:TP=-1.5:LRA=11" -c:a aac -b:a 192k -ar 48000 edit/final.mp4
```

### 7. Self-check (always, before telling the user it is done)
- Duration and size: `ffprobe -v error -show_entries format=duration,size -of csv=p=0 edit/final.mp4`
- No black flashes: `ffmpeg -i edit/final.mp4 -vf blackdetect=d=0.05:pix_th=0.10 -an -f null - 2>&1 | grep black_start`
- No dead air left: the silencedetect command on `final.mp4` (only intended pauses).
- Loudness: `ffmpeg -i edit/final.mp4 -af ebur128=peak=true -f null - 2>&1 | grep -A14 "Summary:"`
- **Look at it:** one frame just before and just after every cut, plus a contact sheet. Read the images:
  ```bash
  ffmpeg -y -i edit/final.mp4 -vf "fps=1,scale=320:-2,tile=6x5" -frames:v 1 edit/checks/sheet.jpg
  ffmpeg -y -ss 12.4 -i edit/final.mp4 -frames:v 1 edit/checks/cut_12.4.jpg
  ```
Report: length, what was cut and why, the checks above, and anything you were not sure about.

## When Clipoox is connected (MCP `clipoox.com/mcp` or `npx clipoox`)

The Timeline at canvas.clipoox.com is a full editor (layers, keyframes, captions, motion presets, colour, audio),
and editing it through MCP is free. Use it when the user wants an editable project rather than a single file, or
wants titles / motion graphics / AI shots.

1. `list_timelines` → `get_timeline` (note `revision`) → read `get_timeline_guide` once for the exact operations.
2. Bring footage in: `add_media` (or `npx clipoox upload file.mp4 --timeline <id> --place`).
3. Plan the cut from the transcript (`transcribe` → `get_transcript`) and beats (`cut_to_beat` for music videos).
4. `edit_timeline` with `base_revision` = the revision you read; on a conflict read again and re-apply.
5. Titles, lower thirds, CTAs, overlays: `search_motion` → `addMotion`. Colour: `colorGrade`.
6. **See your edit without exporting:** `preview_timeline` with `real: true` returns frames drawn like the export
   (free). Check every cut and caption there.
7. Export when the user is happy: `export_video` → `get_export`.

Paid operations (AI B-roll, AI elements, AI motion, AI music, generated images/videos) show a price with
`dry_run`; run them only after the user agrees, with `max_credits` set to that price.

## Hand-off checklist
- [ ] Sources untouched; everything in `edit/`
- [ ] EDL approved by the user
- [ ] Hook ≤ 3 s, no static stretch > 5 s (unless intended)
- [ ] Captions inside safe area, diacritics intact
- [ ] −14 LUFS / ≤ −1.5 dBTP, no clicks at cuts
- [ ] Checks run and reported; `edit/project.md` updated
