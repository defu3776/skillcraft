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
    ├── artifact-quality-gate/
    │   ├── SKILL.md
    │   ├── references/
    │   └── scripts/
    ├── handoff-evidence-collector/
    │   ├── SKILL.md
    │   ├── examples/
    │   ├── references/
    │   └── scripts/
    ├── office-deep-parsing-agent/
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
- `skills/artifact-quality-gate`: Validate non-trivial artifacts for evidence, verification, real-vs-mock clarity, repair path, and human-openable entrypoints.
- `skills/handoff-evidence-collector`: Collect, classify, and validate local Codex/Cursor/project handoff files without rewriting source projects.
- `skills/office-deep-parsing-agent`: Deeply parse mixed Office packages with spreadsheet, document, visual export, OCR, and traceable summary artifacts.
- `skills/video-research-visual-report`: Convert a video into a researched text review plus visual report. It covers transcript extraction, original-source checking, external opinions, adversarial analysis, information images, long images, and PPT-style outputs.

## Featured Release: Office Deep Parsing Agent

`skills/office-deep-parsing-agent` is currently hardened for cross-team Office parsing distribution. Current skill version: `0.3.0`.

Migration note: this skill was formerly named `excel-deep-parsing-agent`. Update local symlinks, install paths, and prompts to `office-deep-parsing-agent`.

Use it when an agent needs to inspect Excel/Office files beyond plain cell extraction, especially workbooks with SAP screenshots, DrawingML shapes, connectors, grouped objects, embedded images, or sheets that need PDF/image rendering before LLM Vision review.

Key behavior:

- MarkItDown is enabled by default as a first-pass text extractor when available.
- Excel structure is parsed with Python where feasible, while visual evidence is preserved through raw media extraction, contact sheets, workbook PDF export, OCR artifacts, and `ocr_results/vision_queue.jsonl`.
- On Windows, workbook PDF export and `.xls -> .xlsx` conversion use LibreOffice first when present, then Microsoft Excel automation through `pywin32` or PowerShell COM.
- For heavy LLM Vision mode, run the pipeline with `--no-ocr` and feed every queued asset in `ocr_results/vision_queue.jsonl` to the external Vision-capable model.
- Malformed, encrypted, corrupt, or extension-mismatched `.xlsx` files fail soft: the pipeline records warnings and still writes the standard artifact set instead of aborting the whole run.
- `structured_data.json`, `workbook_inventory.md`, and `final_summary.md` include machine-readable extraction and Vision readiness status so orchestrators do not treat fail-soft as full extraction success.

Quick validation:

```bash
python skills/office-deep-parsing-agent/scripts/smoke_test.py
python skills/office-deep-parsing-agent/scripts/run_pipeline.py --input-path "<office_file_or_folder>" --output-root "<output_folder>" --no-ocr
```

Required outputs for a healthy run include `file_inventory.md`, `workbook_inventory.md`, `document_inventory.md`, `extracted_markdown/`, `visual_exports/`, `ocr_results/vision_queue.jsonl`, `deep_reading_notes/`, `final_summary.md`, and `structured_data.json`.

## Install A Skill Locally

For Codex-style local skills:

```bash
SKILLCRAFT_ROOT=/path/to/skillcraft
mkdir -p ~/.codex/skills
ln -s "$SKILLCRAFT_ROOT/skills/agent-continuity" ~/.codex/skills/agent-continuity
ln -s "$SKILLCRAFT_ROOT/skills/artifact-quality-gate" ~/.codex/skills/artifact-quality-gate
ln -s "$SKILLCRAFT_ROOT/skills/handoff-evidence-collector" ~/.codex/skills/handoff-evidence-collector
ln -s "$SKILLCRAFT_ROOT/skills/office-deep-parsing-agent" ~/.codex/skills/office-deep-parsing-agent
ln -s "$SKILLCRAFT_ROOT/skills/video-research-visual-report" ~/.codex/skills/video-research-visual-report
```

For other tools, copy or reference the relevant skill folder and its `SKILL.md`.

## Handoff Evidence Collector Example

Use user-supplied roots, repeat `--root` as needed, and pass an explicit validator path when the sibling `agent-continuity` skill is not in the default repository layout:

```bash
python3 skills/handoff-evidence-collector/scripts/handoff_inventory.py \
  --output-root /tmp/handoff-inventory \
  --root /path/to/project-a \
  --root /path/to/project-b \
  --validator-path skills/agent-continuity/scripts/validate_handoff.py
```

The collector is read-only against source roots. It writes `Handoff_Inventory.md` and `work/handoff_inventory.json` under the requested output root.

## Repository Rules

- Keep downloaded videos, generated images, transcripts, and temporary outputs out of git.
- Keep each skill self-contained under `skills/<skill-name>/`.
- Put reusable workflow instructions in `SKILL.md`.
- Put detailed prompts, checklists, and format notes in the skill's `references/`.
- Put deterministic helpers in the skill's `scripts/` when repeated manual code would be error-prone.
