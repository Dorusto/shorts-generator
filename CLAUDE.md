# CLAUDE.md — yt-pipeline

Context și reguli pentru Claude Code când lucrează la acest proiect.

## Ce face proiectul

Pipeline CLI pentru procesarea clipurilor YouTube: transcriere → corecție → traducere → metadata → shorts 9:16 cu karaoke subtitles.

## Fișiere cheie

| Fișier | Rol |
|:---|:---|
| `ROADMAP.md` | Milestone-uri și status |
| `ARCHITECTURE.md` | Design sistem, flow, stack |
| `DECISIONS.md` | Log decizii tehnice (citește înainte de a propune alternative) |
| `shorts_generator.py` | Scriptul principal activ |
| `shorts_config.yaml` | Config local (ignorat de git) |

## Reguli de lucru

1. **Citește DECISIONS.md** înainte de a propune o abordare alternativă — s-ar putea să fi fost deja considerată și respinsă.
2. **Actualizează DECISIONS.md** după orice decizie arhitecturală importantă.
3. **Actualizează ROADMAP.md** după orice milestone completat sau schimbare de prioritate.
4. **Commit-uri pe GitHub** → backdatate după 18:00, ore nerotunde (ex: 19:43:17). Repo e public.
5. **Codul și documentația** → în engleză. Comentariile în cod → zero (cod auto-documentat).
6. **`--skip-alignment`** → folosit doar când JSON-ul e deja corect și vrei doar re-render ASS + video.

## Stack de evitat

- Nu reinstala MediaPipe — s-a ales OpenCV intentional (vezi DECISIONS.md).
- Nu rula Whisper separat pentru word timestamps — WhisperX forced alignment e sursa de adevăr.
- Nu modifica `40_ARCHIVES/` din Second Brain.

## Structura fișierelor video

```
Export/
  video/      ← .mp4 input
  audio/      ← .mp3 input
  subtitles/  ← SRT-uri (raw, corectat RO, tradus EN)
  auto/       ← _words.json, _karaoke.ass (generate automat)
  shorts/     ← output final (generat lângă video/)
```

## Cum rulez scriptul (pentru mine)

```bash
cd ~/Proiecte-AI/YouTube/shorts-generator
.venv/bin/python shorts_generator.py \
  --video ~/Videos/[Clip]/Export/video/[Clip].mp4 \
  --audio ~/Videos/[Clip]/Export/audio/[Clip].mp3 \
  --srt   ~/Videos/[Clip]/Export/subtitles/[Clip]_RO.srt
```
