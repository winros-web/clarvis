# Agen Pejuang Rupiah

Asisten pribadi mandiri untuk urusan cari dan kelola uang. Folder ini berdiri
sendiri: punya `CLAUDE.md` dan memori sendiri, tidak memakai subagen atau
memori dari repo `clarvis`.

## Cara pakai

Buka Claude Code dengan folder ini sebagai direktori kerja. `CLAUDE.md` di sini
akan dibaca otomatis, begitu juga memori di `.claude/memory/`.

Catatan: selama folder ini masih di dalam repo `clarvis`, Claude Code juga
membaca `CLAUDE.md` di root repo (Claude Code membaca ke atas sampai root
repo). Root sudah diberi instruksi supaya tidak mencampur keduanya. Untuk
benar-benar terpisah, pindahkan folder ini jadi repo sendiri:

```bash
# dari komputer, sekali saja
cp -r agen-pejuang-rupiah ~/agen-pejuang-rupiah
cd ~/agen-pejuang-rupiah && git init && git add -A && git commit -m "init"
```

Lalu buat repo kosong di GitHub (mis. `winros-web/agen-pejuang-rupiah`) dan
push ke sana. Setelah itu buka repo baru itu dari aplikasi Claude di iPhone.

## Struktur

```
agen-pejuang-rupiah/
├── CLAUDE.md                 # instruksi utama, dibaca tiap sesi
├── README.md                 # file ini
└── .claude/
    ├── README.md
    └── memory/
        ├── profile.md        # siapa saya
        ├── preferences.md    # cara kerja yang saya suka
        ├── log.md            # catatan keputusan antar sesi
        ├── target.md         # tujuan keuangan
        ├── catatan.md        # buku kas
        └── ide.md            # ide penghasilan tambahan
```

**Jangan simpan nomor rekening, PIN, password, atau data kartu di folder ini.**
Isinya ter-commit ke GitHub.
