---
name: slide-extractor
description: Extract text from PowerPoint (.pptx) slides using smart OCR waterfall logic.
---

# Slide Extractor Skill

This skill allows agents to extract all text content from PowerPoint presentation files, including text embedded in images or slides that are entirely made of images (e.g., from AI tools like NotebookLM).

## Capabilities

- **Direct Extraction**: Quickly extracts text from standard text shapes and tables using `python-pptx`.
- **Waterfall OCR**: Automatically detects images and performs OCR using the best available engine:
  1. **Tesseract OCR**: Fast, local engine (Prioritized).
  2. **EasyOCR**: Advanced AI fallback (Downloads models on first run).
- **Recursive Scan**: Deeply scans grouped shapes to find hidden images.
- **Markdown Output**: Formats extracted text into clean Markdown for easy processing.
- **Google Drive Integration**: Supports downloading and processing files via Drive ID or URL (requires `gog` CLI).

## Usage

Run the script via `uv` to handle dependencies automatically:

```powershell
uv run extract_pptx.py <path_to_pptx_file>
```

### Options

- `--engine tesseract`: Force use of Tesseract engine.
- `--engine easyocr`: Force use of EasyOCR engine.
- `--account <name>`: Specify Google Drive account for `gog` CLI.

## Implementation Details

- **File**: `extract_pptx.py`
- **Dependencies**: `python-pptx`, `pytesseract`, `easyocr`, `Pillow`, `numpy`, `opencv-python-headless`
- **OCR Logic**: 
  - If Tesseract is found in standard Windows paths (`C:\Program Files\Tesseract-OCR\tesseract.exe`), it will be used as the default engine.
  - If Tesseract is missing or `--engine easyocr` is used, it will lazy-load EasyOCR and download models if necessary.
- **GDrive Integration**: Uses the `gog` CLI tool to download presentations from Google Drive into a temporary directory (`slide-extractor`) before processing.

## Best Practices for AI Agents

1. **Verify File Existence**: Ensure the target `.pptx` file exists before calling the script.
2. **Handle Large Files**: For very large presentations with many images, the OCR process may take several minutes.
3. **Analyze Output**: OCR text is prefixed with `[OCR-Tesseract]` or `[OCR-EasyOCR]` and wrapped in blockquotes for context.
