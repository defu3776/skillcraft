# Changelog

## 0.2.2 - RPC visual corpus hardening

- Added Excel ZIP/DrawingML visual preflight for media counts, shape/connectors, object names, and shape text samples.
- Added raw `xl/media` extraction with magic-byte image suffix sniffing, so PNG files with `.tmp` names can still enter OCR.
- Added per-sheet embedded-image contact sheets and `ocr_results/vision_queue.jsonl` for Vision/LLM follow-up.
- Added Tesseract CLI fallback when `pytesseract` is not installed.
- Improved workbook inventory warnings for shape-heavy sheets that require sheet render/PDF for layout semantics.

## 0.2.1 - Release hardening

- Added fail-fast validation for missing or invalid input paths.
- Added collision-safe artifact names based on relative source paths plus short hashes.
- Added collision-safe OCR result names based on relative visual export paths plus short hashes.
- Removed absolute local paths from shared environment, markdown, OCR, and structured result artifacts.
- Added subprocess timeouts for `markitdown` and `soffice` calls.
- Added PDF OCR page cap to avoid unbounded local OCR runs.
- Added cross-platform executable discovery for common LibreOffice and Tesseract install locations.
- Documented offline/proxy dependency installation and legacy Office conversion limits.

## 0.2.0 - Office-wide deep parsing upgrade

- Added Office scope support for `.docx/.doc/.pptx/.ppt` in runtime.
- Added `.xls -> .xlsx` conversion path via LibreOffice for deep spreadsheet parse.
- Added `document_inventory.md` output.
- Upgraded environment probe to include OCR-related modules and executables.
- Upgraded smoke test to core-vs-optional graded checks.

## 0.1.0 - Initial portable release

- Added portable skill runtime under `runtime/`
- Added executable scripts under `scripts/`
  - `run_pipeline.py`
  - `export_visuals.py`
  - `ocr_runner.py`
- Added documentation set:
  - `README.md`
  - `reference.md`
  - `examples.md`
  - `output_template.md`
  - `checklist.md`
  - `troubleshooting.md`
  - `handoff.md`
  - `FAQ.md`
- Added packaging/project files:
  - `VERSION`
  - `CONTRIBUTING.md`
  - `LICENSE`
