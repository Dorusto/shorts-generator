# Learnings — yt-pipeline

Lucruri descoperite pe parcurs care nu sunt evidente din cod sau documentație.

---

## WhisperX

- `whisperx.load_align_model(language_code="ro")` descarcă modelul la prima rulare (~500MB). Ulterior e cacheuit.
- Timestamps din WhisperX sunt relative la audio-ul pasat (0-based). Dacă extragi un segment cu ffmpeg, timestamps sunt deja corecte.
- `return_char_alignments=False` e important pentru performanță — altfel returnează și alignment la nivel de caracter.
- WhisperX instalat cu `--extra-index-url https://download.pytorch.org/whl/cu124` pentru CUDA 12.4 (compatibil cu CUDA 13.3 de pe sistem).

## ffmpeg

- `-ss` înainte de `-i` = fast seek (keyframe-accurate). Suficient pentru talking-head video.
- `h264_nvenc preset p4, cq 23` = echilibru bun calitate/viteză pe RTX 4070 Laptop (~17-19x realtime).
- Filtrul `ass=` din ffmpeg necesită `PlayResX/PlayResY` în ASS — altfel libass asumă 384×288 și fontul iese de 3x mai mare.

## pysubs2 / ASS format

- Culorile în ASS sunt `&HBBGGRR` (nu RGB). Galben = `&H00FFFF` (B=00, G=FF, R=FF).
- `{\c&H00FFFF&}` setează culoarea primară. `{\c&HFFFFFF&}` resetează la alb.
- `alignment=2` în SSAStyle = bottom center (numpad layout).

## OpenCV Haar cascades

- `haarcascade_frontalface_default.xml` e inclus în `cv2.data.haarcascades` — nu necesită download.
- `minSize=(60, 60)` elimină false positives pe frame-uri cu zgomot.
- Sample la fiecare 2s e suficient pentru talking-head videos. Mai des = mai lent fără câștig real.

## Greșeli Whisper cunoscute pe română

- "înrăbdare" → "nerăbdare" (prefix greșit)
- Cuvinte cu cratimă ("task-ul", "n-a", "să-ți") sunt uneori split în tokens separați
- `merge_hyphenated()` în `shorts_generator.py` rezolvă automat split-urile cu cratimă
