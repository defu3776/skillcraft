# QA Checklist (Cross-Team)

Use this checklist to keep delivery quality consistent across teams.

## Before Run

- [ ] Input scope is explicit (single file / directory / recursive scope).
- [ ] Output root path is agreed.
- [ ] Input path exists before execution.
- [ ] Output root is separate from the input directory, or generated output exclusion is acknowledged.
- [ ] Python runtime and dependency source are explicit (venv, internal mirror, or wheelhouse).
- [ ] MarkItDown policy is explicit: default enabled, or intentionally disabled for a scoped test.
- [ ] Visual recognition policy is explicit: local OCR, LLM Vision via `vision_queue.jsonl`, or both.
- [ ] Required outputs are confirmed:
  - [ ] `file_inventory.md` or `file_inventory.csv`
  - [ ] `workbook_inventory.md`
  - [ ] `document_inventory.md`
  - [ ] `extracted_markdown/`
  - [ ] `visual_exports/`
  - [ ] `ocr_results/`
  - [ ] `ocr_results/vision_queue.jsonl`
  - [ ] `deep_reading_notes/`
  - [ ] `final_summary.md`
  - [ ] `structured_data.json`
- [ ] Environment probe result is recorded (python / markitdown / OCR / optional visual-export backend).
- [ ] Failure logging policy is clear (no silent skip).
- [ ] Shared artifacts do not expose absolute local paths, secrets, or environment dumps.

## During Run

- [ ] File inventory includes processed/skipped state and skip reasons.
- [ ] Archive files are recorded as pending (if not expanded by scope).
- [ ] Same-named files in different folders or with different extensions produce distinct artifact names.
- [ ] Workbook structure extraction includes:
  - [ ] sheet order
  - [ ] hidden sheets
  - [ ] merged ranges
  - [ ] formulas and cached values (if available)
  - [ ] comments/hyperlinks
  - [ ] named ranges
  - [ ] validations/conditional formatting
  - [ ] charts/images/shapes/object signals
  - [ ] visual preflight counts for media, DrawingML, shapes, connectors, unsupported media
- [ ] Word/PPT extraction includes:
  - [ ] section/paragraph or slide text
  - [ ] table or note extraction when available
  - [ ] image signals
- [ ] Visual-heavy sheets are exported to raw media/contact sheets/PDF/PNG when layout is needed and the backend exists.
- [ ] OCR/Vision results or queued Vision tasks are mapped to workbook/sheet/page/region.
- [ ] Any parse/tool failure is logged with affected file scope.

## After Run

- [ ] `final_summary.md` exists and each target Excel includes:
  - [ ] file name
  - [ ] file purpose
  - [ ] business/system scope
  - [ ] major sheets
  - [ ] key process flow
  - [ ] input
  - [ ] output
  - [ ] system/screen operations
  - [ ] data update/query/download actions
  - [ ] branch conditions
  - [ ] exception handling
  - [ ] key fields
  - [ ] key OCR/visual conclusions
  - [ ] unconfirmed items
  - [ ] confidence
- [ ] `structured_data.json` includes enough source anchors for traceability.
- [ ] Inferences are marked as `推定`.
- [ ] Unclear points are marked as `不确定`.
- [ ] Conflicting evidence is explicitly listed.
- [ ] No critical conclusion lacks source evidence.

## Release Gate

Only consider the run complete when:

- [ ] All required outputs exist.
- [ ] Artifact names can be mapped back to source rows in `file_inventory.md` / `structured_data.json`.
- [ ] Major failures are either fixed or explicitly reported with impact.
- [ ] Summary is human-readable (not raw extraction fragments only).
