---
name: setup
description: Check and install the free tools the Clipoox video skills use (ffmpeg, ffprobe, faster-whisper CLI, auto-editor, PySceneDetect) and the Clipoox connection. Use when the user runs /clipoox-video:setup, asks to set up video editing, or a video command fails because a tool is missing.
disable-model-invocation: false
---

# Set up the video tools

Reply in the user's language. Install nothing without the user's yes; never use `sudo` unless the user asks.

## 1. Check what is there

Run this and show the user a short table (tool → found / missing, version):

```bash
for t in ffmpeg ffprobe whisper-ctranslate2 auto-editor scenedetect uv; do
  if command -v "$t" >/dev/null 2>&1; then echo "OK      $t  $("$t" --version 2>&1 | head -1)"; else echo "MISSING $t"; fi
done
ffmpeg -hide_banner -filters 2>/dev/null | grep -qE " subtitles " && echo "OK      ffmpeg can burn captions (libass)" || echo "NOTE    this ffmpeg cannot burn captions (no libass)"
```

What each tool is for:
- **ffmpeg / ffprobe** (required): every cut, join, caption, audio and export.
- **whisper-ctranslate2** (recommended): free local transcription with faster-whisper — finds what is said, filler words and pauses. Vietnamese: `--model large-v3`.
- **auto-editor** (optional): removes silence fast; exports to Premiere / Resolve / Final Cut / Kdenlive.
- **scenedetect** (optional): finds shot changes in long footage.
- **uv** (optional): only needed for the `video-use` plug-in.

## 2. Offer to install what is missing

Propose the commands for the user's system, then wait for their OK.

**macOS (Homebrew):**
```bash
brew install ffmpeg auto-editor uv
uv tool install whisper-ctranslate2
uv tool install scenedetect
```
Homebrew's `ffmpeg` cannot burn captions (no libass). If captions are needed, also `brew install ffmpeg-full`
(keg-only, so it does not replace `ffmpeg`) and use `"$(brew --prefix ffmpeg-full)/bin/ffmpeg"` for the caption step.

**Windows (winget, PowerShell):**
```powershell
winget install Gyan.FFmpeg
winget install astral-sh.uv
uv tool install whisper-ctranslate2
uv tool install scenedetect
```
auto-editor: download the Windows binary from https://github.com/WyattBlue/auto-editor/releases, rename it to
`auto-editor.exe` and put it in a folder on PATH (it is no longer published on pip).

**Linux (Debian/Ubuntu):** `sudo apt install ffmpeg` (ask first: it needs sudo). Install uv with
`curl -LsSf https://astral.sh/uv/install.sh | sh` after the user agrees, then the two `uv tool install` lines above.
auto-editor: the Linux binary from the releases page above (`chmod +x`), or Homebrew for Linux.

After installing, run the check again and report the result. A new terminal may be needed for PATH changes.

## 3. Clipoox (optional, free to connect)

This plug-in adds the Clipoox MCP server (`https://clipoox.com/mcp`). The first time a Clipoox tool is used, Claude
Code opens the Clipoox sign-in in the browser — the user signs in there; never ask for a password or code.
With it the AI can edit the user's Timeline projects at canvas.clipoox.com (free), preview them without exporting,
and export MP4s. Paid generation (AI video, images, music) always shows the price first.
Run `/mcp` to see whether `clipoox` is connected.

## 4. Try it

Suggest a first request, run in the folder that holds the footage:
"Dựng các clip này thành một video TikTok 45 giây có phụ đề" / "Cut these clips into a 45-second TikTok with captions".
