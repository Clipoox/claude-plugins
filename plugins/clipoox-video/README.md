# Clipoox Video

Edit video like a professional editor from Claude Code.

- **Skill `clipoox-video-edit`:** a 7-step editing workflow with ffmpeg:
  1. inventory the footage;
  2. brief;
  3. transcript and pauses;
  4. a cut list you approve;
  5. rough cut;
  6. captions, music, loudness and colour;
  7. export and self-check (black frames, dead air, loudness, a frame at every cut).

  Every ffmpeg command in it was run on test media before release.
- **`/clipoox-video:setup`:** checks for ffmpeg, local transcription (faster-whisper), auto-editor and PySceneDetect, and installs what is missing. It always asks first.
- **Clipoox Timeline connector** (`https://clipoox.com/mcp`): the AI edits your Timeline projects on canvas.clipoox.com (free) and previews them without exporting. Sign in on clipoox.com the first time. Paid AI generation always shows the price first.

Dựng video như editor chuyên nghiệp ngay trong Claude Code. Chạy `/clipoox-video:setup` sau khi cài. Hướng dẫn đầy đủ: https://clipoox.com/academy/skill-edit-video-ai-agent

Privacy: https://clipoox.com/chinh-sach-bao-mat · Licence: MIT
