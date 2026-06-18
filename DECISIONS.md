# DECISIONS — yt-pipeline

Format: **[Data] Titlu** — context, opțiuni considerate, decizie, motiv.

---

## [2026-06-18] WhisperX forced alignment în loc de Whisper re-run

**Context:** shorts_generator rulase Whisper a doua oară pe segmentul audio pentru word timestamps, independent de primul run pentru SRT.

**Opțiuni:**
- A) Salvează `_words.json` la primul run Whisper (simplu, dar text potențial greșit)
- B) WhisperX forced alignment pe SRT corectat (text corect, run separat)

**Decizie:** B — WhisperX forced alignment.

**Motiv:** SRT-ul corectat e singura sursă de adevăr. WhisperX aliniază textul corect la audio precis, eliminând greșelile de transcriere din highlight-uri. Un singur loc de corectat.

---

## [2026-06-18] OpenCV Haar cascades în loc de MediaPipe pentru face detection

**Context:** MediaPipe 0.10 a eliminat `mp.solutions` API. Necesita model download + API nou.

**Opțiuni:**
- A) Downgrade MediaPipe la 0.9.x
- B) MediaPipe Tasks API (nou) + model download
- C) OpenCV Haar cascades (built-in, zero dependențe extra)

**Decizie:** C — OpenCV Haar cascades.

**Motiv:** Zero overhead, deja instalat. Un singur offset static per segment (nu tracking dinamic), deci precizia unui model mai sofisticat nu justifică complexitatea.

---

## [2026-06-18] Un singur offset de crop per segment (nu tracking dinamic)

**Context:** Alternativa era să urmărim fața frame cu frame.

**Decizie:** Un offset mediu calculat din sample-uri la fiecare 2s.

**Motiv:** Tracking dinamic produce imagine shakiness. Un offset static e mai stabil vizual pentru talking-head videos unde vorbitorul nu se mișcă mult.

---

## [2026-06-18] uv în loc de pip/virtualenv

**Context:** Gestionare dependențe pentru venv izolat per proiect.

**Decizie:** `uv` pentru toate operațiile de package management.

**Motiv:** 10-100x mai rapid decât pip. Torch + whisperx se instalează semnificativ mai repede.

---

## [2026-06-19] Tăiere la granița de propoziție pentru SRT entries care depășesc segmentul

**Context:** SRT entries care acoperă granița end-time a segmentului conțineau text care nu e pronunțat în clip.

**Opțiuni:**
- A) Tăiere proporțională (număr de cuvinte ≈ fracție din timp)
- B) Tăiere la ultima punctuație înainte de estimarea proporțională

**Decizie:** B — granița de propoziție.

**Motiv:** Opțiunea A lăsa uneori primul cuvânt din propoziția următoare (ex: "Iar"). Opțiunea B taie curat la "cod." și nu include text din afara clipului.
