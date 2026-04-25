---
name: telegram-slide-processor
description: Workflow for processing PowerPoint presentations (PPTX) uploaded via Telegram, cleaning filenames, analyzing content, generating auto-tags, creating an Obsidian inventory note, and uploading to Google Drive.
---

# 🎞️ Telegram Slide Processor Workflow

This skill guides the processing of PowerPoint presentations (PPTX) received via Telegram chat, cleaning their filenames, uploading them to Google Drive, and logging them in the Obsidian Vault.

## 🎯 Core Process: Telegram -> GDrive -> Obsidian Vault

**⚠️ CRITICAL WARNING:** Process files one by one to ensure accurate naming and categorization.

1.  **File Identification & Cleaning:**
    *   **Locate:** Find the uploaded `.pptx` file in the `telegram_files/` directory.
    *   **Cleaning:** Strip any unique hash or ID (e.g., `---e6c1d386-70b8-4a22-a975-5d5113907e00`) from the filename to make it clean.
    *   **GDrive Naming:** Ensure the filename on Google Drive matches the title of the presentation exactly (e.g., `Mengenal Generasi Z: Generasi Digital yang Unik.pptx`).

2.  **Content Analysis (Text Extraction):**
    *   **Analyze Content:** Extract text from the local `.pptx` file.
        *   *Tool:* Run `uv run /home/bety/.ductor/workspace/skills/slide-inventory/scripts/extract_pptx_ocr.py <path-to-cleaned-pptx>`.
    *   **Summarize:** Create an *Executive Brief* summarizing the key points of the presentation.
    *   **Determine Category:** Based on the content, select one of the following categories:
        - `01 - KLA & Evaluasi`
        - `02 - PPA & Penanganan Kasus`
        - `03 - Kesehatan & Parenting`
        - `04 - Edukasi Remaja & Pernikahan Dini`
        - `05 - Administrasi & DAK`
        - `06 - KPHB`
        - `07 - Uncategorized` (Use only as a last resort)

3.  **GDrive Upload & Organization:**
    *   **Upload:** Upload the cleaned `.pptx` file to the specific Google Drive category folder determined in Step 2.
        *   *Tool:* Run `gog drive upload <path-to-cleaned-pptx> --parent <category-folder-id> --json`
    *   **Category Folder IDs (Parent: `1jw8ddZGQElEmQYRK-Su_20YW2xXe8X3c`):**
        - `01 - KLA & Evaluasi`: `1Un5dT8LpUuXounxEZOteHZLWb8e4Ha57`
        - `02 - PPA & Penanganan Kasus`: `151xUe40i5dxjQbJGE1lTD3ijjuSHsOwr`
        - `03 - Kesehatan & Parenting`: `1GNv5Ca1wahr_yTD-hcaXHs20SAQuTClm`
        - `04 - Edukasi Remaja & Pernikahan Dini`: `1HI19IeVaxO3T3HBIm4lNCpgCjDonrw-J`
        - `05 - Administrasi & DAK`: `1KgyXwwPHCwqztZ_BzgZaZTdks9_QsHvs`
        - `06 - KPHB`: `1iueqY2bEpxW-BoGWicT2yAvaCAwdY2T7`
        - `07 - Uncategorized`: `1Yi1hSPKTx5z7ggJAvLUobgvRY8M9wtce`
    *   **Link Generation:** Extract the `webViewLink` from the JSON output of the `gog drive upload` command to use as the GDrive shareable link.

4.  **Auto-Tagging:**
    *   Based on the content analysis, generate appropriate tags:
        - **KLA / Kota Layak Anak** -> `#KLA #KLA-2026`
        - **Evaluasi / evidence** -> `#evaluasi #KLA`
        - **Perlindungan Perempuan** -> `#PPA #perempuan`
        - **Perlindungan Anak** -> `#PPA #anak`
        - **Anggaran / keuangan** -> `#keuangan #anggaran`
        - **Sosialisasi** -> `#sosialisasi`
        - **Rapat / koordinasi** -> `#rapat #koordinasi`
        - **Sambutan / seremonial** -> `#sambutan`
        - **Laporan / capaian** -> `#laporan`
    *   **MANDATORY:** Always append `#slide #presentasi [year]` (e.g., `#2026`).

5.  **Obsidian Inventory Creation:**
    *   **Location:** Save the inventory note in the corresponding category subfolder under `/home/bety/.ductor/workspace/diary/01-Area/Presentasi/`.
        *   *Example:* `diary/01-Area/Presentasi/04 - Edukasi Remaja & Pernikahan Dini/`
    *   **Filename:** `YYYYMMDD-[slug-judul-presentasi].md` (This must match the PPTX base name).
    *   **Template Content:** 
        Fill the note based on `03-Referensi/Template/TPL-Inventory-Presentasi.md`. Include the *Executive Brief* and insert the GDrive link into the frontmatter.

## 📋 Frontmatter Standards

Every generated inventory file MUST contain the following frontmatter structure:

```yaml
---
title: "[Judul Presentasi Sesuai File]"
tags: ["slide", "presentasi", 2026, "tag-lainnya"]
tanggal: YYYY-MM-DD
kegiatan: ""
audiens: ""
gdrive-link: "[Link GDrive yang di-generate]"
topik: ["[Topik Utama 1]", "[Topik Utama 2]"]
status: "selesai"
---
```

## 🛠️ Tools & Resources
- **Upload Tool:** `gog drive upload` (Google CLI)
- **Target GDrive Folder IDs:** See Step 3 above.
- **Obsidian Target Path:** Specific category subfolders inside `diary/01-Area/Presentasi/`.
- **Extraction Script:** `uv run /home/bety/.ductor/workspace/skills/slide-inventory/scripts/extract_pptx_ocr.py`