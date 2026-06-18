# shorts-generator

Automatically generates 9:16 Shorts / Reels from 1920×1080 YouTube videos.

**What it does:**
- Detects face position and computes 608×1080 crop automatically
- Runs forced alignment (WhisperX) on a corrected SRT to get word-level timestamps
- Generates ASS subtitle file with karaoke highlighting (active word = yellow)
- Renders with ffmpeg + h264_nvenc (GPU)

---

## Requirements

- Python 3.10+
- [`uv`](https://github.com/astral-sh/uv)
- `ffmpeg` with nvenc support
- [`whisperx`](https://github.com/m-bain/whisperX) — for forced alignment

---

## Installation

```bash
git clone https://github.com/Dorusto/shorts-generator.git
cd shorts-generator
uv venv
uv pip install -r requirements.txt
```

---

## Usage

### 1. Configure segments

Copy `shorts_config_example.yaml` → `shorts_config.yaml` and fill in your segments:

```yaml
video: "MyVideo.mp4"
audio: "MyVideo.mp3"
srt: "MyVideo_corrected.srt"

segments:
  - name: "Hook"
    start: "00:00:00"
    end: "00:00:54"

  - name: "Reframing"
    start: "00:01:02"
    end: "00:01:52"
```

### 2. Run

**Interactive mode** (prompts for video path):
```bash
.venv/bin/python shorts_generator.py
```

**Direct mode:**
```bash
.venv/bin/python shorts_generator.py \
  --video /path/to/video.mp4 \
  --srt /path/to/corrected.srt
```

### 3. Output

Shorts are saved in a `shorts/` folder next to the video file:
```
shorts/Short1-Hook.mp4
shorts/Short2-Reframing.mp4

auto/Hook_words.json       # word timestamps from forced alignment
auto/Hook_karaoke.ass      # generated karaoke subtitles
```

---

## Expected folder structure

```
Export/
  video/     MyVideo.mp4
  audio/     MyVideo.mp3
  subtitles/ MyVideo_corrected.srt
  shorts/    ← generated output
  auto/      ← intermediate JSON + ASS files
```

---

## Tech stack

| Component | Tool |
|:---|:---|
| Cut + crop + burn subtitles | `ffmpeg` with `h264_nvenc` |
| Word-level alignment | `whisperx` forced alignment on corrected SRT |
| Face detection for crop | `opencv-python` (Haar cascades) |
| ASS subtitle manipulation | `pysubs2` |
