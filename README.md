# Clipoox plug-ins for Claude Code

**Tiếng Việt** · [English](#english)

Bộ plug-in miễn phí để AI (Claude Code) dựng video như một editor. Cài một lần, không phải tự đi cài từng repo.

## Cài đặt

Trong terminal:

```bash
claude plugin marketplace add clipoox/claude-plugins
claude plugin install clipoox-video@clipoox
```

Hoặc trong Claude Code: gõ `/plugin`, chọn **Marketplaces → Add**, nhập `clipoox/claude-plugins`, rồi cài
**clipoox-video**.

Sau đó chạy `/clipoox-video:setup` để AI kiểm tra máy còn thiếu công cụ gì (ffmpeg, công cụ nhận giọng nói…) và cài
giúp bạn (luôn hỏi trước khi cài).

## Có gì trong bộ

| Plug-in | Làm gì | Ghi chú |
|---|---|---|
| **clipoox-video** (khuyên dùng) | Skill dựng video 7 bước: kiểm footage → brief → lời thoại & khoảng lặng → danh sách cắt (bạn duyệt) → dựng → phụ đề, nhạc, màu → xuất + tự kiểm tra. Kèm lệnh `/clipoox-video:setup` và kết nối Timeline Clipoox | Miễn phí. Kết nối Clipoox cần đăng nhập clipoox.com lần đầu |
| **video-use** | Đưa vào thư mục clip thô, nhận `final.mp4`: bỏ từ đệm, chỉnh màu, phụ đề, tự soát | Mã nguồn [browser-use/video-use](https://github.com/browser-use/video-use) (MIT). Cần Python (uv) và key ElevenLabs riêng của bạn (trả phí theo ElevenLabs) |
| **auto-editor** | Tự cắt khoảng lặng, xuất sang Premiere / DaVinci Resolve / Final Cut / Kdenlive | Skill từ [WyattBlue/auto-editor](https://github.com/WyattBlue/auto-editor) (Unlicense). Cần chương trình auto-editor (setup cài giúp) |

Cài thêm: `claude plugin install video-use@clipoox`, `claude plugin install auto-editor@clipoox`.

Hai plug-in bên thứ ba được **khoá ở một phiên bản đã kiểm** (commit SHA trong `.claude-plugin/marketplace.json`) và
tải thẳng từ repo gốc — Clipoox không sửa hay đóng gói lại code của họ. Bản mới chỉ được nâng sau khi Clipoox xem lại.

## Dùng thử

Mở terminal trong thư mục chứa video, chạy `claude`, gõ:

> Dựng các clip này thành một video TikTok 45 giây, có phụ đề và nhạc nền nhẹ.

Bài hướng dẫn đầy đủ: [Skill edit video cho AI agent](https://clipoox.com/academy/skill-edit-video-ai-agent).
Dùng Claude.ai (web/desktop)? Tải [file skill .zip](https://clipoox.com/skills/clipoox-video-edit-v1.zip) và tải lên ở
Customize → Skills.

---

## English

Free plug-ins that let Claude Code edit video like an editor — one install instead of hunting for repos.

```bash
claude plugin marketplace add clipoox/claude-plugins
claude plugin install clipoox-video@clipoox
```

Then run `/clipoox-video:setup`: it checks for ffmpeg, local transcription (faster-whisper), auto-editor and
PySceneDetect and offers to install what is missing (it always asks first).

- **clipoox-video** — the 7-step editing skill (inventory → brief → transcript and pauses → cut list you approve →
  cut → captions, music, colour → export and self-check), `/clipoox-video:setup`, and the Clipoox Timeline connector
  (`https://clipoox.com/mcp`; sign in on clipoox.com the first time).
- **video-use** — [browser-use/video-use](https://github.com/browser-use/video-use) (MIT): raw takes → `final.mp4`.
  Needs Python (uv) and your own ElevenLabs key (paid by ElevenLabs).
- **auto-editor** — skills from [WyattBlue/auto-editor](https://github.com/WyattBlue/auto-editor) (Unlicense): cut
  silence, export to Premiere / Resolve / Final Cut / Kdenlive. Needs the auto-editor program.

Third-party plug-ins are pinned to a reviewed commit and fetched from their own repositories, unmodified.

Licence: MIT for the Clipoox plug-in (this repository). Third-party plug-ins keep their own licences.
