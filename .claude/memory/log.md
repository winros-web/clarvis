# Catatan Antar Sesi

Keputusan, temuan, dan hal penting yang perlu diingat lintas sesi.
Entri terbaru di atas.

---

## 2026-09-23 — Subagen `teknisi-server` (SSH)

- Ditambahkan `.claude/agents/teknisi-server.md`: akses server remote lewat
  `ssh -o BatchMode=yes` memakai alias dari `~/.ssh/config`.
- Aturan: perintah baca boleh langsung; perintah yang mengubah server (restart,
  install, edit, sudo) hanya diusulkan dan menunggu persetujuan; perintah
  berbahaya (reboot, mkfs, ubah firewall/sshd) tidak pernah dijalankan sendiri.
- Tidak pernah menampilkan private key / `.env` / token.

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
