# Skillcraft

Reusable AI skills maintained by chen09.

This repository follows the standard skills repository layout: each directory under `skills/` is one self-contained skill with a `SKILL.md` file and optional bundled resources.

## Structure

```text
skillcraft/
├── README.md
├── LICENSE
├── .gitignore
└── skills/
    ├── agent-continuity/
    │   ├── SKILL.md
    │   ├── references/
    │   └── scripts/
    ├── excel-deep-parsing-agent/
    │   ├── SKILL.md
    │   ├── runtime/
    │   └── scripts/
    └── video-research-visual-report/
        ├── SKILL.md
        ├── agents/
        ├── references/
        └── scripts/
```

## Current Skills

- `skills/agent-continuity`: Externalize long-running agent task state into `handoff.md`, supporting proactive checkpoints and emergency rescue handoffs.
- `skills/excel-deep-parsing-agent`: Deeply parse mixed Office packages with spreadsheet, document, visual export, OCR, and traceable summary artifacts.
- `skills/video-research-visual-report`: Convert a video into a researched text review plus visual report. It covers transcript extraction, original-source checking, external opinions, adversarial analysis, information images, long images, and PPT-style outputs.

## Featured Release: Excel Deep Parsing Agent

`skills/excel-deep-parsing-agent` is currently hardened for cross-team Office parsing distribution. Current skill version: `0.2.4`.

Use it when an agent needs to inspect Excel/Office files beyond plain cell extraction, especially workbooks with SAP screenshots, DrawingML shapes, connectors, grouped objects, embedded images, or sheets that need PDF/image rendering before LLM Vision review.

Key behavior:

- MarkItDown is enabled by default as a first-pass text extractor when available.
- Excel structure is parsed with Python where feasible, while visual evidence is preserved through raw media extraction, contact sheets, workbook PDF export, OCR artifacts, and `ocr_results/vision_queue.jsonl`.
- On Windows, workbook PDF export and `.xls -> .xlsx` conversion use LibreOffice first when present, then Microsoft Excel automation through `pywin32` or PowerShell COM.
- For heavy LLM Vision mode, run the pipeline with `--no-ocr` and feed every queued asset in `ocr_results/vision_queue.jsonl` to the external Vision-capable model.
- Malformed, encrypted, corrupt, or extension-mismatched `.xlsx` files fail soft: the pipeline records warnings and still writes the standard artifact set instead of aborting the whole run.

Quick validation:

```bash
python skills/excel-deep-parsing-agent/scripts/smoke_test.py
python skills/excel-deep-parsing-agent/scripts/run_pipeline.py --input-path "<office_file_or_folder>" --output-root "<output_folder>" --no-ocr
```

Required outputs for a healthy run include `file_inventory.md`, `workbook_inventory.md`, `document_inventory.md`, `extracted_markdown/`, `visual_exports/`, `ocr_results/vision_queue.jsonl`, `deep_reading_notes/`, `final_summary.md`, and `structured_data.json`.

## Install A Skill Locally

For Codex-style local skills:

```bash
mkdir -p ~/.codex/skills
ln -s /Volumes/WDC2T/Project/skillcraft/skills/agent-continuity ~/.codex/skills/agent-continuity
ln -s /Volumes/WDC2T/Project/skillcraft/skills/excel-deep-parsing-agent ~/.codex/skills/excel-deep-parsing-agent
ln -s /Volumes/WDC2T/Project/skillcraft/skills/video-research-visual-report ~/.codex/skills/video-research-visual-report
```

For other tools, copy or reference the relevant skill folder and its `SKILL.md`.

## Repository Rules

- Keep downloaded videos, generated images, transcripts, and temporary outputs out of git.
- Keep each skill self-contained under `skills/<skill-name>/`.
- Put reusable workflow instructions in `SKILL.md`.
- Put detailed prompts, checklists, and format notes in the skill's `references/`.
- Put deterministic helpers in the skill's `scripts/` when repeated manual code would be error-prone.
