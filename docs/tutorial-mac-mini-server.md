# Tutorial: Mac mini sebagai Server Agen, Dikendalikan dari Windows

Tujuan akhirnya:

- **Mac mini** = "rumah" agen. Folder proyek, file agen (`.claude/agents/`),
  dan Claude Code semuanya ada di sini, dan menyala terus.
- **PC Windows** = "remote". Kamu cukup mengetik perintah dari sini (lewat
  browser, aplikasi Claude, atau terminal). Semua pekerjaan dijalankan di Mac mini.

```
[PC Windows] ──(internet / Wi-Fi rumah)──> [Mac mini: Claude Code + folder agen]
```

Perkiraan waktu: 45–60 menit untuk pertama kali.

Yang dibutuhkan:

- Mac mini dengan macOS terbaru, tersambung internet (lebih baik pakai kabel LAN).
- Akun Claude berlangganan (Pro/Max) — dipakai untuk login Claude Code.
- Akun GitHub (repo `winros-web/clarvis` ada di sana).
- PC Windows 10/11.

> Cara membaca tutorial ini: blok kode diketik di **Terminal**. Judul setiap
> langkah menyebut di mesin mana perintahnya dijalankan: **[MAC]** atau **[WINDOWS]**.

---

## Bagian 1 — Siapkan Mac mini supaya layak jadi server

### 1.1 [MAC] Jangan biarkan Mac tidur

Kalau Mac tidur, agen ikut berhenti.

1. Buka **System Settings** (ikon gerigi) → **Energy** (di macOS lama:
   *Energy Saver*).
2. Nyalakan:
   - **Prevent automatic sleeping when the display is off**
   - **Wake for network access**
   - **Start up automatically after a power failure**
3. Layar boleh mati. Yang penting Mac-nya tetap menyala.

### 1.2 [MAC] Login otomatis setelah restart (opsional tapi disarankan)

**System Settings → Users & Groups → Automatically log in as** → pilih akunmu.

> Kalau opsi ini abu-abu, FileVault (enkripsi disk) sedang aktif. Tidak apa-apa:
> biarkan FileVault menyala (lebih aman), dan setiap kali Mac restart kamu perlu
> login sekali di depan Mac atau lewat Screen Sharing.

### 1.3 [MAC] Aktifkan SSH (Remote Login)

SSH adalah "pintu" agar Windows bisa masuk ke Mac lewat terminal.

1. **System Settings → General → Sharing**.
2. Nyalakan **Remote Login**.
3. Klik ikon **(i)** di sebelahnya → *Allow access for*: **Only these users** →
   pastikan hanya akunmu.
4. Catat tulisan di bawahnya, misalnya `ssh johan@192.168.1.20`.
   - `johan` = **username Mac**-mu
   - `192.168.1.20` = **IP Mac** di jaringan rumah

Sekalian nyalakan **Screen Sharing** di halaman yang sama. Ini berguna kalau
nanti kamu perlu melihat layar Mac dari jauh.

### 1.4 [MAC] Buka Terminal

Tekan `Cmd + Space`, ketik **Terminal**, lalu Enter. Semua langkah [MAC]
berikutnya diketik di sini.

### 1.5 [MAC] Pasang alat dasar

Alat pengembang dari Apple (termasuk `git`):

```bash
xcode-select --install
```

Akan muncul jendela. Klik **Install** dan tunggu sampai selesai.

Homebrew (untuk memasang aplikasi lewat terminal):

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Di akhir instalasi, Homebrew menampilkan **"Next steps"** berisi 2–3 baris perintah
(biasanya diawali `echo ... >> ~/.zprofile`). **Salin dan jalankan baris-baris
itu**, lalu tutup Terminal dan buka lagi.

Cek apakah berhasil:

```bash
brew --version
```

Pasang tmux (supaya agen tetap jalan walau jendela ditutup), GitHub CLI, dan Bun:

```bash
brew install tmux gh
curl -fsSL https://bun.sh/install | bash
```

### 1.6 [MAC] Pasang Claude Code

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Tutup lalu buka lagi Terminal, kemudian cek:

```bash
claude --version
```

### 1.7 [MAC] Ambil folder agen dari GitHub

Login ke GitHub (pilih *GitHub.com → HTTPS → Login with a web browser*, lalu
ikuti kodenya):

```bash
gh auth login
```

Buat folder kerja dan clone repo:

```bash
mkdir -p ~/agen
cd ~/agen
gh repo clone winros-web/clarvis
cd clarvis
bun install
```

Sekarang folder agenmu ada di **`~/agen/clarvis`** di Mac mini.

### 1.8 [MAC] Login Claude Code (sekali saja)

```bash
cd ~/agen/clarvis
claude
```

Ikuti petunjuknya: pilih login dengan akun Claude, browser akan terbuka, lalu
setujui. Setelah masuk, coba ketik `halo`. Kalau dijawab, berarti berhasil.
Keluar dengan mengetik `/exit`.

---

## Bagian 2 — Jalankan agen sebagai "server" yang selalu siap

### 2.1 [MAC] Jalankan di dalam tmux

tmux ibarat "jendela terminal yang tidak ikut tertutup". Program di dalamnya
tetap jalan walau Terminal ditutup atau koneksi SSH terputus.

```bash
tmux new -s agen
```

Di dalam tmux:

```bash
cd ~/agen/clarvis
claude remote-control
```

Perintah ini membuat Claude Code di Mac mini bisa dikendalikan dari aplikasi
Claude atau browser di perangkat lain yang login dengan akun yang sama.

Keluar dari tmux **tanpa mematikan agen**: tekan `Ctrl + B`, lepas, lalu tekan `D`.

Perintah tmux yang perlu diingat:

| Perintah | Fungsi |
|---|---|
| `tmux new -s agen` | membuat sesi bernama `agen` |
| `Ctrl+B` lalu `D` | keluar, tapi sesi tetap jalan |
| `tmux attach -t agen` | masuk lagi ke sesi |
| `tmux ls` | melihat sesi yang sedang jalan |

### 2.2 [MAC] Skrip start sekali klik (untuk setelah restart)

Setelah Mac restart, tmux ikut mati. Buat skrip supaya menyalakannya lagi cukup
satu perintah:

```bash
cat > ~/start-agen.sh <<'EOF'
#!/bin/zsh
tmux has-session -t agen 2>/dev/null && { echo "Agen sudah jalan."; exit 0; }
tmux new -d -s agen -c ~/agen/clarvis 'claude remote-control'
echo "Agen dinyalakan. Masuk dengan: tmux attach -t agen"
EOF
chmod +x ~/start-agen.sh
```

Setelah restart, cukup jalankan `~/start-agen.sh`, dari depan Mac atau lewat SSH
dari Windows (Bagian 3.2).

---

## Bagian 3 — Mengendalikan dari PC Windows

Ada tiga cara. **Cara A paling mudah untuk sehari-hari.** Cara B dan C
untuk perawatan atau kalau ingin tampilan editor.

### 3.1 Cara A — Lewat browser atau aplikasi Claude (paling mudah)

1. [WINDOWS] Buka **claude.ai/code** di browser (atau aplikasi Claude/Claude Code),
   login dengan akun Claude yang **sama** dengan di Mac mini.
2. Sesi dari Mac mini (Remote Control) muncul di daftar sesi. Buka sesinya.
3. Ketik perintahmu seperti biasa. Semua dikerjakan di `~/agen/clarvis` di Mac mini.

Cara ini juga bisa dipakai dari HP.

### 3.2 Cara B — Lewat terminal Windows (SSH)

**[WINDOWS] Buka terminal:** klik Start, ketik **Terminal** (atau *PowerShell*),
lalu Enter.

**Uji koneksi** (ganti `johan` dan IP sesuai catatan di langkah 1.3):

```powershell
ssh johan@192.168.1.20
```

- Pertama kali muncul pertanyaan `Are you sure you want to continue connecting`:
  ketik `yes`.
- Masukkan password akun Mac. Huruf yang diketik memang tidak terlihat, itu normal.
- Kalau prompt berubah jadi seperti `johan@Mac-mini ~ %`, kamu sudah "di dalam" Mac.
  Ketik `exit` untuk keluar.

**Supaya tidak perlu mengetik password (SSH key):**

```powershell
ssh-keygen -t ed25519
```

Tekan Enter tiga kali (lokasi default, tanpa passphrase; atau isi passphrase
kalau ingin lebih aman). Lalu kirim kunci publiknya ke Mac:

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh johan@192.168.1.20 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

Masukkan password Mac sekali lagi. Setelah ini `ssh johan@192.168.1.20` tidak
akan menanyakan password.

**Beri nama pendek** supaya cukup mengetik `ssh macmini`:

```powershell
notepad $env:USERPROFILE\.ssh\config
```

Kalau ditanya membuat file baru, pilih **Yes**. Isi dengan:

```
Host macmini
    HostName 192.168.1.20
    User johan
```

Simpan. Pastikan nama filenya `config` **tanpa** `.txt`. Sekarang:

```powershell
ssh macmini
```

**Pemakaian sehari-hari lewat SSH:**

```bash
ssh macmini
~/start-agen.sh          # nyalakan agen kalau Mac habis restart
tmux attach -t agen      # lihat sesi agen yang sedang jalan
# atau jalankan Claude Code langsung:
cd ~/agen/clarvis && claude
```

### 3.3 Cara C — VS Code di Windows, file di Mac (opsional)

1. [WINDOWS] Pasang **VS Code**, lalu extension **Remote - SSH** dan **Claude Code**.
2. Tekan `F1` → ketik **Remote-SSH: Connect to Host** → pilih `macmini`.
3. *File → Open Folder* → `~/agen/clarvis`.
4. Buka panel Claude Code. Semua file dan perintah berjalan di Mac mini, tapi
   tampilannya ada di Windows.

---

## Bagian 4 — Akses dari luar rumah (opsional)

IP `192.168.x.x` hanya berlaku di jaringan rumah yang sama.

- **Cara A (claude.ai/code)** sudah bisa dari mana saja tanpa pengaturan tambahan.
- Untuk **Cara B/C** dari luar rumah, pasang **Tailscale** (gratis untuk pribadi):
  1. Pasang Tailscale di Mac mini dan di PC Windows, login dengan akun yang sama.
  2. Di aplikasi Tailscale, lihat nama atau IP Mac (formatnya `100.x.x.x`).
  3. Ganti `HostName` di `~/.ssh/config` Windows dengan IP Tailscale itu.

> **Jangan** membuka port 22 di router ke internet. Tailscale jauh lebih aman.

---

## Bagian 5 — Menambah atau mengubah agen

File agen ada di Mac mini: `~/agen/clarvis/.claude/agents/`. Cara termudah
untuk menambah agen: minta langsung ke Claude lewat Cara A, misalnya
*"buatkan agen baru untuk ..."*. Setelah agen baru dibuat, restart sesi supaya
agennya terbaca:

```bash
tmux kill-session -t agen
~/start-agen.sh
```

Supaya perubahan tidak hilang dan tersimpan di GitHub, minta Claude untuk
commit dan push, atau jalankan sendiri di Mac:

```bash
cd ~/agen/clarvis
git add -A && git commit -m "feat: tambah agen baru" && git push
```

---

## Bagian 6 — Kalau ada masalah

| Gejala | Penyebab umum | Solusi |
|---|---|---|
| `ssh: connect ... timed out` | Mac tidur, IP berubah, atau beda jaringan | Cek Mac menyala; cek IP lagi di Sharing → Remote Login; di luar rumah pakai Tailscale |
| `Permission denied` | Username/password salah, atau user tidak diizinkan | Cek langkah 1.3 (*Only these users*) |
| `command not found: claude` | Terminal belum di-refresh setelah instalasi | Tutup lalu buka lagi Terminal, atau `source ~/.zprofile` |
| `command not found: brew` | Langkah "Next steps" Homebrew terlewat | Jalankan ulang baris `echo ... >> ~/.zprofile` dari output instalasi |
| Sesi tidak muncul di claude.ai/code | Remote Control mati atau beda akun | `ssh macmini` → `tmux ls`; kalau kosong jalankan `~/start-agen.sh`; pastikan akun sama |
| IP Mac sering berubah | Router membagi IP acak | Atur *DHCP reservation* untuk Mac di router, atau pakai Tailscale |

## Keamanan singkat

- Jangan tempel password, token, atau private key di chat dengan Claude.
- Simpan rahasia proyek di file `.env` di Mac (sudah di-*ignore* git), bukan di repo.
- Pakai SSH key, dan jangan buka port router ke internet.
- Pasang update macOS secara berkala (System Settings → General → Software Update).
