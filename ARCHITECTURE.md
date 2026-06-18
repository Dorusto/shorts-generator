# ARCHITECTURE — yt-pipeline

## Overview

Pipeline CLI pentru procesarea clipurilor YouTube: transcriere → corecție → traducere → metadata → shorts.

```
Export/
  video/   MyClip.mp4        ← input video (1920×1080)
  audio/   MyClip.mp3        ← input audio (pentru Whisper/WhisperX)
  subtitles/
    MyClip.srt               ← Whisper raw output
    MyClip_RO.srt            ← corectat manual
    MyClip_EN.srt            ← tradus
  auto/
    MyClip_words.json        ← word timestamps (Whisper sau WhisperX)
    Hook_karaoke.ass         ← ASS generat per segment
  shorts/
    Short1-Hook.mp4          ← output final 608×1080
  metadata/
    video_metadata.txt       ← titlu, descriere, tags, capitole
```

---

## Scripturi

| Script | Input | Output | Tool |
|:---|:---|:---|:---|
| `transcribe.py` (planned) | `.mp3` | `.srt` + `_words.json` | Whisper turbo |
| `correct_srt.py` | `.srt` | `_RO.srt` | DeepSeek API |
| `translate_srt.py` | `_RO.srt` | `_EN.srt` | DeepSeek API |
| `analyze_srt.py` | `_RO.srt` + `.mp4` | `video_metadata.txt` | DeepSeek API |
| `shorts_generator.py` | `.mp4` + `.mp3` + `_RO.srt` | `Short[N]-[Name].mp4` | WhisperX + ffmpeg |
| `pipeline.py` (planned) | config + Export folder | tot | orchestrator |

---

## Stack tehnic

| Componentă | Tool | Note |
|:---|:---|:---|
| Transcriere | `openai-whisper turbo` | instalat via pipx |
| Forced alignment | `whisperx` | aliniază text corectat la audio |
| Face detection | `opencv` Haar cascades | un offset per segment, nu tracking |
| Subtitle render | `pysubs2` → ASS | PlayRes 608×1080, font Arial Bold |
| Video render | `ffmpeg h264_nvenc` | RTX 4070 Laptop, preset p4, cq 23 |
| AI corecție/traducere/metadata | DeepSeek API | via openai-compatible client |
| Package management | `uv` | venv per proiect |

---

## Decizii de design

→ vezi `DECISIONS.md`

---

## Pipeline flow (planned)

```
[Export clip]
     ↓
transcribe.py ──→ MyClip.srt + _words.json
     ↓
[Review & correct SRT manual]
     ↓
correct_srt.py ──→ MyClip_RO.srt
     ↓
translate_srt.py ──→ MyClip_EN.srt
     ↓
analyze_srt.py ──→ video_metadata.txt
     ↓
shorts_generator.py ──→ Short[N].mp4 (citește _words.json sau rulează WhisperX)
```
