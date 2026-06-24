# Converti Audio in MP3 (Light)

Web app **100% client-side** per trasformare un file audio pesante in un **MP3 leggero**
(48 kHz · 128 kbps · CBR · stereo) pronto da inviare su WhatsApp, email o messaggistica.

- Nessun server, nessun upload, nessuna registrazione.
- I file restano nel browser dell'utente.
- Ospitabile su **GitHub Pages** senza configurazioni speciali.

## Come funziona (motore ibrido, automatico)

1. **Via veloce** — l'audio viene decodificato con la **Web Audio API** (`decodeAudioData`),
   ricampionato a 48 kHz stereo (`OfflineAudioContext`) e codificato in MP3 da
   **[lamejs](https://github.com/zhuker/lamejs)** in un **Web Worker**. Leggera e istantanea.
2. **Fallback universale** — se il browser non sa decodificare quel formato, l'app carica
   da sola **[ffmpeg.wasm](https://github.com/ffmpegwasm/ffmpeg.wasm)** e converte *qualsiasi*
   formato. Il core pesante (~24 MB) viene scaricato **solo quando serve davvero** e poi resta
   in cache.
3. L'utente scarica l'MP3 o lo invia con la **Web Share API** (dove supportata, es. iPhone).

L'utente non sceglie né configura nulla: il passaggio tra i due motori è invisibile.

## Formati in ingresso

Praticamente **qualsiasi formato audio** (e l'audio estratto da file video):
WAV, AIFF, MP3, M4A, AAC, FLAC, OGG, OPUS, WMA, AMR, AC3, ALAC, CAF, MKA, ecc.

- Formati comuni → via veloce (lamejs), nessun download.
- Formati che il browser non gestisce (es. FLAC/OGG/OPUS su iPhone) → fallback ffmpeg.wasm.

## Cross-origin isolation

ffmpeg.wasm richiede `SharedArrayBuffer`, che necessita degli header COOP/COEP. Poiché
GitHub Pages non li imposta, `coi-serviceworker.min.js` li abilita lato client tramite un
service worker (attivazione automatica, un solo reload silenzioso alla prima visita).

## File

- `index.html` · `style.css` · `script.js` — l'app
- `worker.js` — codifica MP3 in background (via veloce)
- `lame.min.js` — encoder MP3 (vendored)
- `coi-serviceworker.min.js` — abilita l'isolamento cross-origin su GitHub Pages
- `vendor/ffmpeg/` — ffmpeg.wasm self-hosted (motore di fallback)
- `favicon.svg` · `og-image.svg` — icona e anteprima social

## Deploy su GitHub Pages

```bash
git add .
git commit -m "Audio to MP3 Light"
git push
```

Poi: repo → Settings → Pages → Branch `main` / root.

---

Creato da [Simone Castellan](https://simonecastellan.com)
