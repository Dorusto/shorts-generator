# ROADMAP — yt-pipeline

## Status curent
Pipeline parțial funcțional. `shorts_generator.py` e în producție (testat pe Lenea). Celelalte scripturi existente dar neintegrate în pipeline unificat.

---

## Milestone 1 — Unificare repo ✅ (parțial)
- [x] `shorts_generator.py` — crop 9:16, WhisperX alignment, karaoke ASS, ffmpeg nvenc
- [x] GitHub repo `shorts-generator` (de redenumit `yt-pipeline`)
- [ ] Mută `correct_srt.py`, `translate_srt.py`, `analyze_srt.py` în repo
- [ ] Redenumește repo → `yt-pipeline`
- [ ] README actualizat cu toate scripturile

## Milestone 2 — Whisper integrat în pipeline
- [ ] `transcribe.py` — rulează Whisper o singură dată, salvează SRT + `_words.json` simultan
- [ ] `shorts_generator.py` citește `_words.json` existent (nu mai rulează WhisperX dacă există)
- [ ] Elimină necesitatea WhisperX dacă `_words.json` e proaspăt

## Milestone 3 — `pipeline.py` orchestrator
- [ ] Un singur script care orchestrează tot în pași:
  1. `transcribe` → SRT + words JSON
  2. `correct` → SRT corectat (+ prompt manual review)
  3. `translate` → SRT tradus EN
  4. `analyze` → titlu, descriere, capitole, tags
  5. `shorts` → render toate segmentele din config
- [ ] Config unificat `pipeline_config.yaml`
- [ ] Flag `--from-step N` pentru a relua de la un pas specific

## Milestone 4 — Calitate shorts
- [ ] Verificare și îmbunătățire face detection (Haar cascades → model mai bun dacă necesar)
- [ ] Opțiune font customizabil din config (mărime, culoare highlight, font family)
- [ ] Opțiune pentru mai multe cuvinte per linie (LINE_WORDS din config)
- [ ] Preview rapid (10s) înainte de render complet

## Milestone 5 — Upload & metadata
- [ ] Integrare `analyze_srt.py` în pipeline → `video_metadata.txt` automat
- [ ] Opțional: YouTube Data API upload automat

---

## Known issues
- Face detection (Haar cascades) poate rata sau centra greșit — de verificat per clip
- `correct_srt.py` nu prinde toate greșelile Whisper (ex: "înrăbdare" → "nerăbdare")
- Fraze care depășesc granița segmentului sunt tăiate proporțional (aproximare)
