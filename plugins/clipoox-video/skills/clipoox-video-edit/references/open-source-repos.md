# Open-source tools for AI video editing (checked November 2026)

Use these when the user's machine has them, or suggest installing one when it clearly fits the job.
Stars are rounded, from GitHub on 2026-11-09.

## Agent skills (install into Claude Code / Codex / Cursor)
| Repo | What it is | Install |
|---|---|---|
| [browser-use/video-use](https://github.com/browser-use/video-use) (★ 31k, MIT) | Skill that turns a folder of raw takes into `final.mp4`: removes fillers, grades, fades every cut, burns captions, checks its own output | see its install.md |
| [remotion-dev/skills](https://github.com/remotion-dev/skills) (★ 6.6k) | Official Remotion skills: create, markup, captions, maps, render | `npx skills add remotion-dev/skills` |
| [heygen-com/hyperframes](https://github.com/heygen-com/hyperframes) (★ 81k, Apache-2.0) | HTML + CSS → deterministic MP4, built for agents | `npx hyperframes skills update` |
| [digitalsamba/claude-code-video-toolkit](https://github.com/digitalsamba/claude-code-video-toolkit) (★ 2.5k, MIT) | Claude Code workspace: script, voice-over, music, visuals, render | clone, then `/setup`, `/video` |
| [WyattBlue/auto-editor](https://github.com/WyattBlue/auto-editor) (★ 5.8k, Unlicense) | Cuts silence/motionless parts; exports to Premiere, Resolve, FCP, Kdenlive | `npx skills add WyattBlue/auto-editor` |
| [anthropics/skills](https://github.com/anthropics/skills) (★ 190k) | The Agent Skills format and examples — read to write your own skill | `/plugin marketplace add anthropics/skills` |

## Engines and libraries
| Repo | Use it for |
|---|---|
| [FFmpeg/FFmpeg](https://github.com/FFmpeg/FFmpeg) | Everything: cut, join, scale, filters, audio, encode |
| [Zulko/moviepy](https://github.com/Zulko/moviepy) (MIT) | Python editing scripts (compositing, text, simple effects) |
| [PyAV-Org/PyAV](https://github.com/PyAV-Org/PyAV) / [kkroening/ffmpeg-python](https://github.com/kkroening/ffmpeg-python) | FFmpeg from Python, frame-level or filter graphs |
| [remotion-dev/remotion](https://github.com/remotion-dev/remotion) | Videos as React components (check its licence for company use) |
| [motion-canvas/motion-canvas](https://github.com/motion-canvas/motion-canvas) (MIT) | Code-driven motion graphics |
| [ManimCommunity/manim](https://github.com/ManimCommunity/manim) (MIT) | Explainer animations (maths, diagrams) |
| [mifi/editly](https://github.com/mifi/editly) (MIT) | Declarative JSON → video (slideshows, simple edits) |

## Understanding footage
| Repo | Use it for |
|---|---|
| [SYSTRAN/faster-whisper](https://github.com/SYSTRAN/faster-whisper) (MIT) | Fast local transcription (Vietnamese works well with `large-v3`) |
| [m-bain/whisperX](https://github.com/m-bain/whisperX) (BSD-2) | Word-level timestamps + speaker labels — best for caption timing |
| [openai/whisper](https://github.com/openai/whisper) (MIT) | Reference transcription model, writes SRT/VTT |
| [snakers4/silero-vad](https://github.com/snakers4/silero-vad) (MIT) | Voice activity detection (where someone speaks) |
| [Breakthrough/PySceneDetect](https://github.com/Breakthrough/PySceneDetect) (BSD-3) | Shot/scene detection, split by scene |
| [facebookresearch/sam2](https://github.com/facebookresearch/sam2) (Apache-2.0) | Track and mask an object through a video |
| [ZhengPeng7/BiRefNet](https://github.com/ZhengPeng7/BiRefNet) (MIT) | High-quality background removal |

## Desktop editors (hand the cut to a human)
[mifi/lossless-cut](https://github.com/mifi/lossless-cut) (fast lossless trims) ·
[KDE/kdenlive](https://github.com/KDE/kdenlive) · [OpenShot/openshot-qt](https://github.com/OpenShot/openshot-qt) ·
[mltframework/mlt](https://github.com/mltframework/mlt) (the engine under Kdenlive/Shotcut)

## Downloading references
[yt-dlp/yt-dlp](https://github.com/yt-dlp/yt-dlp) — download only videos the user owns or may use.

Always check each project's licence before using it in a commercial product.
