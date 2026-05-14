# Troubleshooting

## `python` command cannot run

Symptoms:

- `python` returns launcher error or command not found.
- smoke test reports missing core package such as `openpyxl`.

Actions:

1. verify interpreter path
2. activate the intended virtual environment or run with an explicit executable path
3. install requirements into that exact interpreter
4. re-run `smoke_test.py` and `run_pipeline.py`

## Dependency install fails behind proxy or offline

Symptoms:

- `pip install` cannot reach PyPI.
- packages time out or fail TLS/proxy checks.

Actions:

1. use the company-approved package mirror, proxy, or wheelhouse
2. retry with `--no-index --find-links "<wheelhouse_dir>"` when using offline wheels
3. keep the install command in the handoff notes so another team can reproduce it

## Input path fails immediately

Symptoms:

- `run_pipeline.py` exits with `input path does not exist`.

Actions:

1. verify the path in the same shell/session that runs the script
2. avoid relying on shell aliases or unmapped network drives
3. create the output directory separately from the input directory

## `markitdown` not found

Symptoms:

- markdown extraction status is `skipped` with `markitdown CLI not found`.

Actions:

1. install markitdown
2. ensure executable is in PATH
3. rerun pipeline or markdown stage

## Visual export missing PDF

Symptoms:

- no sheet PDF export output.
- `ocr_results/vision_queue.jsonl` contains `blocked_missing_render_backend`.

Actions:

1. verify `soffice` availability
2. confirm LibreOffice can open the source file manually if conversion keeps failing
3. keep embedded-image extraction as fallback where available
4. review `workbook_inventory.md` visual preflight counts for shapes/connectors/unsupported media
5. log warning and affected workbook

## OCR outputs empty

Symptoms:

- OCR files exist but texts are empty or weak.

Actions:

1. check OCR backend and language data
2. if `pytesseract` is unavailable, verify the `tesseract` executable is installed and visible
3. increase export resolution before OCR
4. split large visuals into tiles and rerun

## Workbook parse failure

Symptoms:

- workbook has warnings in analysis output.

Actions:

1. record exact file and failure reason
2. continue processing remaining files
3. mark summary sections as `不确定` where evidence is missing

## Duplicate-looking artifact names

Symptoms:

- output files include source extension and an 8-character hash.

Explanation:

- this is expected hardening behavior; it prevents `sample.xlsx`, `sample.docx`, and `folder/sample.xlsx` from overwriting each other.
