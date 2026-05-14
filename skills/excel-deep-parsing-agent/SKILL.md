---
name: excel-deep-parsing-agent
description: Deeply parses Office files (.xlsx/.xlsm/.xls/.csv/.docx/.doc/.pptx/.ppt), including workbook/cell/object analysis and document structure analysis, and performs visual understanding by exporting pages/sheets to PDF/PNG and applying OCR/Vision to images, screenshots, and flow diagrams. Use when users need traceable business-level interpretation, not just text extraction.
disable-model-invocation: true
---

# Excel Deep Parsing Agent

## Purpose

Use this skill when the user wants deep, business-meaningful analysis of Office files, not a shallow text dump.

## Required Behavior

1. Inventory first, then analyze.
2. Parse spreadsheet workbook -> sheet -> cell/range with coordinates.
3. Parse Word/PPT document structure (sections, tables/slide text, notes, image signals).
4. Capture structural elements (hidden sheets, merged cells, formulas, comments, hyperlinks, named ranges, validations, conditional formats, charts, images, shapes, objects).
5. For Excel visuals, preflight the Office ZIP (`xl/media`, DrawingML, anchors, shape text, object names) before relying on parser output.
6. For visual-heavy sheets/pages, export raw media/contact sheets/PDF where available, run OCR, and write `ocr_results/vision_queue.jsonl` for Vision/LLM follow-up.
7. Merge all evidence into traceable outputs.
8. Mark uncertainty explicitly (`推定`, `不确定`) instead of guessing.

## Environment Probe and Failure Logging

Before full run, probe required capabilities and record status in logs:

- Python runtime
- markitdown availability
- Excel automation availability (if needed)
- LibreOffice/soffice availability (if needed)
- OCR backend availability

If a capability is missing:

- do not silently skip
- keep processing with available paths
- write explicit warnings and affected-file scope in intermediate outputs
- do not record absolute local paths or environment secrets in shared artifacts

Input/output safety:

- fail fast when `--input-path` does not exist or is not a regular file/directory
- use a separate output directory; if it is inside the input tree, generated output is excluded from the next inventory pass
- artifact filenames are derived from relative source paths plus a short hash to avoid collisions between same-named files
- legacy `.xls/.doc/.ppt` parsing depends on LibreOffice conversion and must be reported as limited when conversion is unavailable
- executable discovery checks PATH plus common macOS and Windows install locations for LibreOffice and Tesseract

## Standard Workflow

1. Build `file_inventory` for input scope.
2. Run markdown extraction (`markitdown`) per Office file where applicable.
3. Build workbook inventory and document inventory.
4. Export visual pages/regions, embedded media, and contact sheets for OCR/Vision when layout is important.
5. Run OCR and map outputs to workbook/sheet/page/region anchors; queue remaining Vision/LLM tasks explicitly.
6. Fuse all evidence and produce final human summary + structured JSON.

## Executable Scripts

Use these scripts as defaults instead of ad-hoc one-liners:

- `scripts/run_pipeline.py`: End-to-end run with environment probe log.
- `scripts/export_visuals.py`: Export workbook visuals (embedded images/PDF).
- `scripts/ocr_runner.py`: OCR pass on visual exports.

These scripts use bundled runtime code under `runtime/` so the skill can be shared across repositories without requiring `tools/excel_deep_parser`.

Install baseline dependencies:

```bash
python -m pip install -r .cursor/skills/excel-deep-parsing-agent/scripts/requirements.txt
```

For offline or proxied environments, install from an approved mirror or wheelhouse:

```bash
python -m pip install --no-index --find-links "<wheelhouse_dir>" -r .cursor/skills/excel-deep-parsing-agent/scripts/requirements.txt
```

Run full pipeline:

```bash
python .cursor/skills/excel-deep-parsing-agent/scripts/run_pipeline.py --input-path "<input_path>" --output-root "<output_root>"
```

Run only visual export:

```bash
python .cursor/skills/excel-deep-parsing-agent/scripts/export_visuals.py --workbook "<workbook_path>" --output-dir "<visual_output_dir>"
```

Run only OCR stage:

```bash
python .cursor/skills/excel-deep-parsing-agent/scripts/ocr_runner.py --visual-root "<visual_output_dir>" --ocr-output "<ocr_output_dir>" --backend local
```

## Required Output Set

- `file_inventory.md` (or `file_inventory.csv`)
- `workbook_inventory.md`
- `document_inventory.md`
- `extracted_markdown/`
- `visual_exports/`
- `ocr_results/`
- `ocr_results/vision_queue.jsonl`
- `deep_reading_notes/`
- `final_summary.md`
- `structured_data.json`

## Final Summary Minimum Fields (Per Excel)

- file name
- file purpose
- business/system scope
- major sheets
- key process flow
- input
- output
- system/screen operations
- data update/query/download actions
- branch conditions
- exception handling
- key fields
- key OCR/visual conclusions
- unconfirmed items
- confidence

## Quality Bar

- Accuracy before speed.
- Do not rely on one tool.
- Do not ignore images/objects.
- Keep conclusions traceable to workbook/sheet/cell or visual page/region.
- If parse fails, explain reason and impact.

## Additional Reference

For full detailed policy text, read [reference.md](reference.md) and apply it directly.

Practical companion files:

- [README.md](README.md)
- [examples.md](examples.md)
- [output_template.md](output_template.md)
- [troubleshooting.md](troubleshooting.md)
- [checklist.md](checklist.md)
- [handoff.md](handoff.md)
- [FAQ.md](FAQ.md)
- [CHANGELOG.md](CHANGELOG.md)
- [CONTRIBUTING.md](CONTRIBUTING.md)
