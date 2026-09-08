# Catatan Antar Sesi

Keputusan, temuan, dan hal penting yang perlu diingat lintas sesi.
Entri terbaru di atas.

---

## 2026-09-08 — Agen Pejuang Rupiah (folder mandiri)

- Dibuat folder `agen-pejuang-rupiah/` di root repo: asisten pribadi yang
  **berdiri sendiri**, punya `CLAUDE.md` dan `.claude/memory/` sendiri.
- Sengaja **tidak** dijadikan subagen dan tidak terhubung dengan subagen lain
  di `.claude/agents/`. Sempat dibuat sebagai subagen, lalu dibongkar.
- Untuk memutus total dari clarvis, folder ini bisa dipindah jadi repo sendiri
  (lihat `agen-pejuang-rupiah/README.md`).
- Dibuat dari sesi iPhone; branch `claude/iphone-agent-creation-3y15u8`.

## 2026-09-03 — Subagen asisten pribadi

- Dibuat 5 subagen di `.claude/agents/`: `sekretaris`, `peneliti`, `penulis`,
  `reviewer-kode`, `arsiparis`.
- Batasan yang disepakati: `sekretaris` **tidak boleh mengirim email**, hanya
  membuat draf; `reviewer-kode` hanya membaca, tidak mengubah file.
- Bentuk agen lain yang belum dibuat dan bisa menyusul: Routine terjadwal
  (jalan sendiri tanpa sesi), skill/slash command, dan hook.

## 2026-09-03 — Setup memori

- Dibuat `CLAUDE.md` di root + folder `.claude/memory/` berisi profil, preferensi,
  konteks proyek, dan log ini.
- Claude ditetapkan sebagai **asisten pribadi**, bukan hanya asisten coding.
- `.gitignore` diberi pengecualian agar `.claude/memory/` ikut ter-commit
  (container sesi bersifat sementara — yang tidak di-commit akan hilang).
