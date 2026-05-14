# Change Summary

## What Changed

- Added RPC-style Excel visual corpus handling: ZIP/DrawingML preflight, media extension sniffing, raw media extraction, per-sheet contact sheets, shape/object text sampling, and Vision queue output.
- Added Tesseract CLI fallback so local OCR can run when `pytesseract` is absent but the `tesseract` executable is available.
- Added input validation so missing or invalid `--input-path` fails with exit code `2` instead of generating empty success artifacts.
- Added collision-safe artifact naming for markdown, visual exports, attachment staging, OCR JSON, and deep-reading notes.
- Added collision-safe OCR result names based on relative visual export paths so same-named PDFs/images in different export folders do not overwrite each other.
- Changed shared runtime artifacts to use relative source/output paths and removed absolute interpreter paths from environment probes.
- Added a subprocess wrapper with a 120-second timeout for `markitdown` and `soffice`.
- Added a 25-page cap for local PDF OCR.
- Added executable discovery across PATH plus common macOS and Windows install locations for LibreOffice and Tesseract.
- Escaped Markdown table cells in `file_inventory.md`.
- Clarified dependencies, optional tool behavior, proxy/offline install options, and legacy `.xls/.doc/.ppt` conversion limits.
- Updated `VERSION` to `0.2.2` and added `0.2.1`/`0.2.2` changelog entries.

## Why It Changed

- Prevent false-positive successful runs when the input path is wrong.
- Preserve traceability when a package contains same-named Office files.
- Make outputs safer to share across teams by avoiding local path leakage.
- Avoid unbounded local processing on untrusted or malformed Office/PDF files.
- Make setup and degradation behavior explicit for teams with different Python, proxy, LibreOffice, OCR, or markdown-extraction environments.
- Preserve object-heavy Excel evidence that normal openpyxl parsing cannot represent, especially SAP screenshots, flowcharts, connectors, and DrawingML shapes.

## Validation Results

- Syntax check: PASS
  - Command: `<python> -m py_compile runtime/pipeline.py scripts/run_pipeline.py scripts/export_visuals.py scripts/ocr_runner.py scripts/smoke_test.py`
- Smoke test: PASS
  - Command: `<python> scripts/smoke_test.py`
  - Result: exit `0`; core imports passed; optional missing dependencies were reported, not hidden.
- RPC Excel sample run: PASS
  - Command: `<python> scripts/run_pipeline.py --input-path <rpc RPA-184 xlsx> --output-root <output_root> --no-markitdown`
  - Result: exit `0`; 53 media entries, 9 drawing XML files, 200 shapes, 22 connectors, 111 OCR successes, and 113 Vision queue tasks were recorded.
- Missing input failure check: PASS
  - Command: `<python> scripts/run_pipeline.py --input-path <missing_input> --output-root <output_root>`
  - Result: exit `2`; output root was not created.
- Mixed Office sample run: PASS
  - Command: `<python> scripts/run_pipeline.py --input-path <sample_input> --output-root <sample_output>`
  - Result: exit `0`; required artifacts existed.
- Standalone visual export script: PASS
  - Command: `<python> scripts/export_visuals.py --workbook <sample_input>/sample.xlsx --output-dir <visual_output>`
  - Result: exit `0`; log written; no absolute workbook/output path in log.
- Standalone OCR script: PASS
  - Command: `<python> scripts/ocr_runner.py --visual-root <sample_output>/visual_exports --ocr-output <ocr_output> --backend local`
  - Result: exit `0`; OCR JSON written with `success` via Tesseract CLI fallback because `pytesseract` is unavailable in this environment.
- Artifact existence checklist: PASS
  - `file_inventory.md`, `workbook_inventory.md`, `document_inventory.md`, `extracted_markdown/`, `visual_exports/`, `ocr_results/`, `deep_reading_notes/`, `final_summary.md`, and `structured_data.json` all existed.
- Absolute-path leak check: PASS
  - Command: absolute-path scan over `<sample_output>`
  - Result: no matches.
