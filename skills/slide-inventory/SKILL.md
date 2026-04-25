---
name: slide-inventory
description: Workflow for managing PowerPoint presentations (PPTX) using Gemini and Google Workspace extension. This skill covers ownership checking, move/copy logic to specific GDrive category folders, auto-tagging based on content, and creating inventory notes in Obsidian with Master Checklist synchronization.
---

# 🎞️ Slide Inventory Workflow

This skill guides the processing of PowerPoint presentations (PPTX) from receipt to archival, categorization, and inventory tracking using Gemini and Google Workspace Extension.

## 🎯 Core Process PPTX -> Obsidian Vault

**⚠️ CRITICAL WARNING:** ONLY process ONE (1) checklist item per request. Do not attempt to process multiple presentations at once, as this will trigger Google Workspace API rate limits.

1.  **Target Selection & Cleaning:**
    *   Target File: Locate the file on Google Drive using the ID/Link provided in the `Master Checklist Inventory.md`.
    *   GDrive Naming: Ensure the filename follows the format `[Judul Ringkas].pptx`.
    *   Local/Inventory Naming: `YYYYMMDD-[slug-judul-presentasi].md`

2.  **Content Analysis (Text Extraction):**
    *   **Primary:** Use the EXACT tool name `mcp_google-workspace_slides.getText` (note the underscore before slides, NOT a hyphen) to extract text from slides.
    *   **Fallback (OCR):** If `getText` is insufficient (e.g., Canva/Image-based slides), use `uv run /home/bety/.ductor/workspace/skills/slide-inventory/scripts/extract_pptx_ocr.py`.
    *   Summarize content into an *Executive Brief* for the inventory note.

3.  **GDrive Management (Ownership & Organization):**
    *   **Ownership Check:** Use `mcp_google-workspace_drive.getMetadata` to check the owner.
    *   **Move vs Copy:**
        - Owned by `bety@pkrbt.id` -> **Move** directly to the category folder.
        - Shared/Owned by others -> **Copy** (download & upload) to `bety@itstoni.com`, then **Move** to the category folder.
    *   **Target Folders (Parent: `1jw8ddZGQElEmQYRK-Su_20YW2xXe8X3c`):**
        - `01 - KLA & Evaluasi`: `1Un5dT8LpUuXounxEZOteHZLWb8e4Ha57`
        - `02 - PPA & Penanganan Kasus`: `151xUe40i5dxjQbJGE1lTD3ijjuSHsOwr`
        - `03 - Kesehatan & Parenting`: `1GNv5Ca1wahr_yTD-hcaXHs20SAQuTClm`
        - `04 - Edukasi Remaja & Pernikahan Dini`: `1HI19IeVaxO3T3HBIm4lNCpgCjDonrw-J`
        - `05 - Administrasi & DAK`: `1KgyXwwPHCwqztZ_BzgZaZTdks9_QsHvs`
        - `06 - KPHB`: `1iueqY2bEpxW-BoGWicT2yAvaCAwdY2T7`
        - `07 - Uncategorized`: `1Yi1hSPKTx5z7ggJAvLUobgvRY8M9wtce`

4.  **Auto-Tagging:**
    *   Generate tags based on content (e.g., #KLA, #PPA, #evaluasi, #laporan).
    *   Always add mandatory tags: `#slide #presentasi [year]`.

5.  **Obsidian Inventory Creation:**
    *   Location: Save in the corresponding subfolder under `01-Area/Presentasi/`.
    *   Template: Use `03-Referensi/Template/TPL-Inventory-Presentasi.md`.
    *   Priority: Use categories 01-06; use `07 - Uncategorized/` only as a last resort.

6.  **Master Checklist Sync:**
    *   **MANDATORY:** After successful creation, you MUST physically edit the file `/home/bety/.ductor/workspace/diary/01-Area/Presentasi/Master Checklist Inventory.md`.
    *   Find the specific item you just processed and use a file editing tool (like `replace`) to change its status from `[ ]` to `[x]`. Do not just say you did it; you must execute the file replacement.

## 🏷️ Auto-Tagging & Topic Rules

Match detected content to these tags:
- **KLA / Kota Layak Anak** -> `#KLA #KLA-2026`
- **Evaluasi / evidence** -> `#evaluasi #KLA`
- **Perlindungan Perempuan** -> `#PPA #perempuan`
- **Perlindungan Anak** -> `#PPA #anak`
- **Anggaran / keuangan** -> `#keuangan #anggaran`
- **Sosialisasi** -> `#sosialisasi`
- **Rapat / koordinasi** -> `#rapat #koordinasi`
- **Sambutan / seremonial** -> `#sambutan`
- **Laporan / capaian** -> `#laporan`

## 📋 Frontmatter Standards

```yaml
---
title: ""
tags: ["slide", "presentasi", 2026]
tanggal: YYYY-MM-DD
kegiatan: ""
audiens: ""
gdrive-link: ""
topik: []
status: "selesai"
---
```

## 🛠️ Tools & Resources
- **Template:** `03-Referensi/Template/TPL-Inventory-Presentasi.md`
- **GDrive Parent ID:** `1jw8ddZGQElEmQYRK-Su_20YW2xXe8X3c`
- **OCR Script:** `uv run skills/slide-inventory/scripts/extract_pptx_ocr.py`
