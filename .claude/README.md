# .claude/

Konfigurasi & memori Claude Code untuk repositori ini.

- `memory/` — memori persisten (ikut di-commit, lihat pengecualian di `.gitignore`)
  - `profile.md` — profil pengguna
  - `preferences.md` — preferensi kerja
  - `project-clarvis.md` — konteks teknis proyek
  - `log.md` — catatan antar sesi
  - `pejuang-rupiah/` — folder kerja agen `pejuang-rupiah` (target, buku kas, ide)
- `agents/` — definisi subagen (satu file `.md` per agen)

Memori utama ada di `CLAUDE.md` pada root repo; file di sini di-import dari sana.

**Jangan simpan API key, token, atau password di folder ini** — isinya ter-commit.
File lain di `.claude/` (mis. `settings.local.json`) tetap diabaikan git.
