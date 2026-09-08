# Catatan Antar Sesi

Keputusan, temuan, dan hal penting yang perlu diingat lintas sesi.
Entri terbaru di atas.

---

## 2026-09-08 — Agen Pejuang Rupiah

- Dibuat subagen `pejuang-rupiah` di `.claude/agents/` untuk urusan keuangan
  pribadi dan ide penghasilan: buku kas, target, hitung harga jasa.
- Folder kerjanya `.claude/memory/pejuang-rupiah/` (`target.md`, `catatan.md`,
  `ide.md`). Agen wajib baca-tulis ke sana, bukan hanya menjawab di chat.
- Batasan: tidak menyimpan nomor rekening/PIN/kartu; tidak mengeksekusi
  transaksi.
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
