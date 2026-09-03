# Proyek: clarvis

**Apa ini**: Hook processor TypeScript/Bun yang mengubah pesan Claude Code menjadi
notifikasi suara bergaya JARVIS ("Sir, I have completed project auth...").

Repo: `winros-web/clarvis` (upstream: `nickpending/clarvis`) — status alpha v0.1.0.

## Stack

- Runtime: **Bun** (bukan Node langsung), TypeScript 5.8+
- Package manager: pnpm
- Test: `bun test` (ada juga vitest sebagai devDependency)
- Lint: ESLint + @typescript-eslint
- Config: TOML via `@iarna/toml` (`config.toml`, contoh di `config.toml.example`)
- TTS eksternal: [lspeak](https://github.com/nickpending/lspeak)

## Struktur `src/`

| File | Tanggung jawab |
|---|---|
| `index.ts` | entry point, orkestrasi alur hook → LLM → speaker |
| `config.ts` | baca & validasi `config.toml` |
| `hookParser.ts` | parse payload hook dari Claude Code |
| `transcript.ts` | ambil/olah transcript sesi |
| `metadata.ts` | parse baris metadata (project/topic, mode) |
| `llm.ts` | ringkas pesan jadi 1–3 kalimat gaya JARVIS |
| `speaker.ts` | kirim teks ke lspeak / TTS |
| `logger.ts` | logging |
| `types.ts` | tipe bersama |

Test ada di `tests/unit/` dan `tests/integration/`.

## Perintah

```bash
bun run dev         # watch mode
bun run run         # jalankan sekali
bun test            # test
bun run lint        # eslint src
bun run type-check  # tsc --noEmit
bun run build       # build ke dist/
```

## Catatan / gotcha

- Butuh API key (OpenAI wajib, ElevenLabs opsional) — simpan di env/config, **jangan** di-commit.
- Delay ~27 detik pada pemakaian lspeak pertama (loading model ML).
- Metadata harus dikeluarkan Claude secara manual; belum otomatis.
- Urutan field metadata pernah jadi sumber bug output satu kalimat (lihat commit `0cc2b62`).
