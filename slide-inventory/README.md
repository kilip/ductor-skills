# 🎞️ Slide Inventory Skill

Skill ini digunakan oleh Gem (Gemini) untuk memproses, mengekstrak teks (termasuk OCR), mengkategorikan, dan membuat inventory dari file presentasi (PPTX) milik Ibu Bos.

## 🛠️ Persyaratan Sistem (System Requirements)

Agar skill ini, khususnya fitur **OCR (Optical Character Recognition)**, berjalan dengan lancar, server/komputer perlu menginstal beberapa aplikasi dasar.

### 1. Install Tesseract OCR (System Level)
Script OCR membutuhkan mesin Tesseract beserta paket bahasa Indonesia dan Inggris.
Jalankan command berikut di terminal Ubuntu/Debian Anda:
```bash
sudo apt update
sudo apt install tesseract-ocr tesseract-ocr-ind tesseract-ocr-eng
```

### 2. Install `uv` (Python Package Manager)
Script Python pada skill ini menggunakan `uv` untuk manajemen dependency yang sangat cepat dan otomatis (tanpa perlu repot membuat `venv` manual). 
Jika belum terinstall, jalankan:
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source $HOME/.cargo/env
```

---

## 🚀 Cara Penggunaan (Usage)

**⚠️ PERINGATAN PENTING:** Gem hanya boleh memproses **SATU (1)** item checklist per perintah/request. Jangan menyuruh Gem memproses banyak presentasi sekaligus karena akan menyebabkan *Rate Limit* pada API Google Workspace.

Skill ini dirancang untuk berjalan secara otomatis saat Ibu Bos memberikan perintah kepada Gem di chat, namun script OCR-nya juga bisa dijalankan secara manual.

### A. Penggunaan via Chat (Otomatis oleh Gem)
Arahkan Gem ke ID file Google Drive yang terdaftar di `Master Checklist Inventory.md` dan berikan perintah seperti:
*   _"Gem, tolong kerjakan item nomor 1 di Master Checklist Inventory."_
*   _"Gem, buatkan inventory dari presentasi dengan ID GDrive xxx, jangan lupa pindahkan ke kategori yang pas."_

Gem akan secara otomatis:
1. Mendownload/mengekstrak teks file dari GDrive menggunakan tool `mcp_google-workspace_slides.getText` (pastikan pakai underscore, bukan strip sebelum slides) atau `mcp_google-workspace_drive.downloadFile`.
2. Jika teks kurang/berupa gambar, Gem akan otomatis memanggil script OCR via `uv run` secara lokal (setelah file didownload).
3. Mengecek *ownership* GDrive (Move atau Copy).
4. Membuat catatan di Obsidian dan menggunakan tool *replace* file untuk benar-benar mengubah isi `/home/bety/.ductor/workspace/diary/01-Area/Presentasi/Master Checklist Inventory.md` dari `[ ]` menjadi `[x]`.

### B. Menjalankan Script OCR Secara Manual (Terminal)
Jika Anda ingin mengekstrak teks dan gambar dari PPTX secara manual lewat terminal, Anda tidak perlu menginstall `pip install` manual. Cukup gunakan `uv run` dan `uv` akan otomatis mendownload modul yang dibutuhkan (`python-pptx`, `pytesseract`, `Pillow`) saat eksekusi berjalan.

**Command Dasar:**
```bash
uv run /home/bety/.ductor/workspace/skills/slide-inventory/scripts/extract_pptx_ocr.py <path_ke_file_pptx_yang_sudah_didownload>
```

**Command dengan Output File:**
Untuk menyimpan hasil OCR ke dalam file teks (.txt):
```bash
uv run /home/bety/.ductor/workspace/skills/slide-inventory/scripts/extract_pptx_ocr.py /path/to/downloaded/file.pptx --output /home/bety/.ductor/workspace/output_to_user/hasil_ekstrak.txt
```

---

## 📂 Struktur Direktori Skill

```text
slide-inventory/
├── SKILL.md                   # Aturan utama, prompt, dan instruksi untuk Gem (Gemini)
├── README.md                  # Dokumentasi setup & penggunaan ini
├── scripts/
│   └── extract_pptx_ocr.py    # Script Python untuk OCR gambar di dalam slide PPTX
├── assets/                    # Folder untuk aset tambahan (jika ada)
└── references/                # Folder untuk referensi tambahan (jika ada)
```

## ⚙️ Dependencies Python (Otomatis oleh uv)
Sebagai informasi, script `extract_pptx_ocr.py` secara otomatis menggunakan inline-metadata untuk memanggil library berikut saat dieksekusi:
- `python-pptx` (Membaca file PowerPoint)
- `pytesseract` (Python wrapper untuk Tesseract OCR)
- `Pillow` (Memproses/membuka image blob dari slide)