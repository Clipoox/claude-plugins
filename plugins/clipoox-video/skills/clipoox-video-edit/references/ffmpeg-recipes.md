# ffmpeg recipes (tested with ffmpeg 7/8)

All commands write to `edit/`. Replace file names and times. Times are seconds.
`-crf 18` ≈ visually lossless for intermediates; `-crf 20–23` for delivery.

## Reframe

**16:9 → 9:16, centre crop** (subject in the middle):
```bash
ffmpeg -y -i in.mp4 -vf "crop=ih*9/16:ih,scale=1080:1920,setsar=1" -c:v libx264 -crf 18 -c:a copy edit/vertical.mp4
```

**16:9 → 9:16 with a blurred background** (keep the whole frame):
```bash
ffmpeg -y -i in.mp4 -filter_complex \
 "[0:v]split=2[a][b];[a]scale=1080:1920:force_original_aspect_ratio=increase,crop=1080:1920,boxblur=20:2[bg];\
[b]scale=1080:-2[fg];[bg][fg]overlay=(W-w)/2:(H-h)/2,setsar=1[v]" \
 -map "[v]" -map 0:a? -c:v libx264 -crf 18 -c:a copy edit/vertical_blur.mp4
```

**Off-centre subject:** change the crop x, e.g. `crop=ih*9/16:ih:iw*0.62-ih*9/32:0` keeps a subject at 62 % width.

## Punch-in (jump-cut zoom, 115 %)
```bash
ffmpeg -y -i seg.mp4 -vf "crop=iw/1.15:ih/1.15,scale=1920:1080,setsar=1" -c:v libx264 -crf 18 -c:a copy edit/seg_punch.mp4
```

## Speed
2× (video and audio):
```bash
ffmpeg -y -i in.mp4 -filter_complex "[0:v]setpts=0.5*PTS[v];[0:a]atempo=2.0[a]" -map "[v]" -map "[a]" -c:v libx264 -crf 18 -c:a aac edit/fast.mp4
```
Slow motion 0.5× without audio: `-vf "setpts=2.0*PTS" -an`. For smooth slow motion from 24/30 fps footage add
`minterpolate=fps=60:mi_mode=mci` (slow to render; footage shot at 60–120 fps looks far better).

## Transitions between two clips (same size and fps)
Cross-dissolve 0.5 s starting 4.5 s into clip A (A is 5 s long):
```bash
ffmpeg -y -i a.mp4 -i b.mp4 -filter_complex \
 "[0:v][1:v]xfade=transition=fade:duration=0.5:offset=4.5,format=yuv420p[v];[0:a][1:a]acrossfade=d=0.5[a]" \
 -map "[v]" -map "[a]" -c:v libx264 -crf 18 -c:a aac edit/ab.mp4
```
Other `transition=` values: `wipeleft`, `slideup`, `circleopen`, `dissolve`, `zoomin`, `fadeblack`, `smoothleft`.
Use them sparingly — straight cuts are the default in professional edits.

## Audio

**Music under a voice, ducked while the voice speaks:**
```bash
ffmpeg -y -i voice.mp4 -i music.mp3 -filter_complex \
 "[1:a]volume=0.35,aresample=48000[m];[0:a]aresample=48000,asplit=2[v1][v2];\
[m][v1]sidechaincompress=threshold=0.03:ratio=8:attack=20:release=400[duck];\
[v2][duck]amix=inputs=2:duration=first:normalize=0[a]" \
 -map 0:v -map "[a]" -c:v copy -c:a aac -b:a 192k edit/with_music.mp4
```

**Clean up a voice** (high-pass rumble, gentle compression):
```bash
ffmpeg -y -i in.mp4 -af "highpass=f=80,acompressor=threshold=-20dB:ratio=3:attack=10:release=200" -c:v copy -c:a aac edit/voice_clean.mp4
```
`afftdn` reduces steady background noise (`afftdn=nf=-25`). Strong noise: denoise in a dedicated tool first.

**Loudness for social (two passes is most accurate; one pass is fine for drafts):**
```bash
ffmpeg -y -i in.mp4 -af "loudnorm=I=-14:TP=-1.5:LRA=11" -c:v copy -c:a aac -b:a 192k -ar 48000 edit/loud.mp4
```

## Captions

SRT from a transcript (whisper writes it). Burn in with a style (needs ffmpeg built with libass —
`ffmpeg -filters | grep subtitles`; the Homebrew core build may lack it, static builds from ffmpeg.org/download have it):
```bash
ffmpeg -y -i in.mp4 -vf "subtitles=edit/subs.srt:force_style='FontName=Be Vietnam Pro,FontSize=16,Bold=1,PrimaryColour=&H00FFFFFF,OutlineColour=&H00000000,BorderStyle=1,Outline=3,Shadow=0,Alignment=2,MarginV=70'" \
 -c:v libx264 -crf 18 -c:a copy edit/captioned.mp4
```
ASS colours are `&HAABBGGRR` (blue-green-red). For SRT input libass draws on a 288-line canvas, so `FontSize` and
`MarginV` scale with the video: `MarginV=70` sits about a quarter of the height above the bottom edge on any size —
right for 9:16, where app buttons cover the bottom ~20 %. Short-form style: split lines to 1–3 words in the SRT
before burning. Check one frame with diacritics (ấ, ặ, ỗ, ự) after burning.

Soft subtitles (viewer can turn off; mp4 uses mov_text):
```bash
ffmpeg -y -i in.mp4 -i edit/subs.srt -map 0 -map 1 -c copy -c:s mov_text -metadata:s:s:0 language=vie edit/soft_subs.mp4
```

## Colour
Small, global moves (contrast, saturation, warmth):
```bash
ffmpeg -y -i in.mp4 -vf "eq=contrast=1.06:saturation=1.08:gamma=0.98,colorbalance=rs=0.03:bs=-0.03" -c:v libx264 -crf 18 -c:a copy edit/graded.mp4
```
A LUT from your colourist: `-vf "lut3d=look.cube"`. Grade after cutting, on the joined file, so shots stay consistent.

## Overlays
Logo top-right at 80 % opacity:
```bash
ffmpeg -y -i in.mp4 -i logo.png -filter_complex "[1:v]format=rgba,colorchannelmixer=aa=0.8,scale=180:-1[l];[0:v][l]overlay=W-w-40:40" -c:v libx264 -crf 18 -c:a copy edit/logo.mp4
```
Picture-in-picture (screen recording + face cam bottom-right):
```bash
ffmpeg -y -i screen.mp4 -i cam.mp4 -filter_complex "[1:v]scale=480:-2[c];[0:v][c]overlay=W-w-40:H-h-40:shortest=1" -map 0:a? -c:v libx264 -crf 18 -c:a aac edit/pip.mp4
```

## Analysis helpers
```bash
# Pauses (candidate cuts)
ffmpeg -i in.mp4 -af silencedetect=noise=-35dB:d=0.4 -f null - 2>&1 | grep -E "silence_(start|end)"
# Shot changes
ffmpeg -i in.mp4 -vf "select='gt(scene,0.3)',showinfo" -an -f null - 2>&1 | grep -o "pts_time:[0-9.]*"
# Black frames
ffmpeg -i in.mp4 -vf blackdetect=d=0.05:pix_th=0.10 -an -f null - 2>&1 | grep black_start
# Loudness report
ffmpeg -i in.mp4 -af ebur128=peak=true -f null - 2>&1 | grep -A14 "Summary:"
# Contact sheet (1 frame / s, 6×5 grid) and a single frame
ffmpeg -y -i in.mp4 -vf "fps=1,scale=320:-2,tile=6x5" -frames:v 1 edit/checks/sheet.jpg
ffmpeg -y -ss 12.4 -i in.mp4 -frames:v 1 edit/checks/frame_12.4.jpg
```

## Delivery presets
| Target | Video | Audio |
|---|---|---|
| TikTok / Reels / Shorts | 1080×1920, 30 fps, `-crf 20 -preset slow -pix_fmt yuv420p -movflags +faststart` | AAC 192k 48 kHz, −14 LUFS |
| YouTube 1080p | 1920×1080, source fps, `-crf 18 -preset slow -pix_fmt yuv420p -movflags +faststart` | AAC 256k 48 kHz, −14 LUFS |
| Archive / hand-off | ProRes: `-c:v prores_ks -profile:v 3 -pix_fmt yuv422p10le` (.mov) | PCM `-c:a pcm_s24le` |
