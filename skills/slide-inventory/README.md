# 🎞️ Slide Inventory Skill

This skill is used by Gem (Gemini) to process, extract text (including OCR), categorize, and create an inventory of presentation files (PPTX) belonging to "Ibu Bos".

## 🛠️ System Requirements

To ensure this skill, specifically the **OCR (Optical Recognition)** features, runs smoothly, the server or computer needs several basic applications.

### 1. Install Tesseract OCR (System Level)
The OCR script requires the Tesseract engine along with Indonesian and English language packs.
Run the following command on your Ubuntu/Debian terminal:
```bash
sudo apt update
sudo apt install tesseract-ocr tesseract-ocr-ind tesseract-ocr-eng
```
*(For Windows users: Use `choco install tesseract -y`)*

### 2. Install `uv` (Python Package Manager)
Python scripts in this skill use `uv` for ultra-fast and automatic dependency management.
If not yet installed, run:
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source $HOME/.cargo/env
```

---

## 🚀 Usage

**⚠️ IMPORTANT WARNING:** Gem should only process **ONE (1)** checklist item per request. Do not ask Gem to process multiple presentations at once, as it may cause *Rate Limits* on the Google Workspace API.

This skill is designed to run automatically when "Ibu Bos" gives a command to Gem in chat, but the OCR script can also be run manually.

### A. Usage via Chat (Automated by Gem)
Point Gem to the Google Drive file ID listed in the `Master Checklist Inventory.md` and give a command like:
*   _"Gem, please process item number 1 in the Master Checklist Inventory."_
*   _"Gem, create an inventory for the presentation with GDrive ID xxx, and don't forget to move it to the appropriate category."_

Gem will automatically:
1. Download/extract text from the GDrive file using `mcp_google-workspace_slides.getText` or `mcp_google-workspace_drive.downloadFile`.
2. If text is missing or in image format, Gem will automatically call the local OCR script via `uv run` (after the file is downloaded).
3. Check GDrive ownership (Move or Copy).
4. Create a note in Obsidian and use the file *replace* tool to update the status in `Master Checklist Inventory.md` from `[ ]` to `[x]`.

### B. Running the OCR Script Manually (Terminal)
If you want to extract text and images from a PPTX manually via terminal, you don't need to install packages manually. Just use `uv run`, and `uv` will automatically download the required modules (`python-pptx`, `pytesseract`, `Pillow`) during execution.

**Basic Command:**
```bash
uv run scripts/extract_pptx_ocr.py <path_to_downloaded_pptx_file>
```

**Command with Output File:**
To save OCR results to a text file (.txt):
```bash
uv run scripts/extract_pptx_ocr.py /path/to/file.pptx --output result.txt
```

---

## 📂 Skill Directory Structure

```text
slide-inventory/
├── SKILL.md                   # Core rules, prompts, and instructions for Gem (Gemini)
├── README.md                  # This setup & usage documentation
├── scripts/
│   └── extract_pptx_ocr.py    # Python script for OCRing images within PPTX slides
├── assets/                    # Additional assets folder
└── references/                # Additional references folder
```

## ⚙️ Python Dependencies (Automated by uv)
The `extract_pptx_ocr.py` script automatically uses inline-metadata to call the following libraries during execution:
- `python-pptx` (PowerPoint reading)
- `pytesseract` (Tesseract OCR wrapper)
- `Pillow` (Image processing)