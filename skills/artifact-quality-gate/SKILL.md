---
name: artifact-quality-gate
description: Validate reports, receipts, skill candidates, handoffs, handoff-first inventories, research outputs, and implementation summaries for evidence, verification, real-vs-mock clarity, repair path, and human-openable entrypoints. Use when a non-trivial Codex artifact may be too thin, generic, unverified, hard for a human to accept, or claims self-distillation/continuity results that should be grounded in handoff evidence.
---

# Artifact Quality Gate

## Workflow

1. Identify artifact type: report, skill candidate, handoff, receipt, code summary, research output, visual report, or fixture.
2. Score the artifact with the quality table:
   - goal answered;
   - evidence bound;
   - structure;
   - input/process/output contract;
   - verification;
   - real/mock distinction;
   - repair/rollback;
   - human entrypoint.
   For handoff-first artifacts, specifically check whether the artifact cites `Handoff_Inventory.md` or `handoff_inventory.json`, labels execution mode (`real_execution`, `mock`, `fixture`, `dry_run`, or `candidate_only`), and includes before/after or validator results when it claims a repair or quality improvement.
3. If score is below 75, return a rework task list instead of accepting the artifact.
4. If score is 75-89, mark usable with targeted gaps.
5. If score is 90+, mark ready for review.

## Output

Return JSON plus a short Markdown summary. Never claim real validation when only mock, fixture, dry-run, or candidate-only validation was performed.

## Evidence Handling

Use concrete local paths, repo-relative paths, command outputs, source dates, handoff inventory artifacts, or `evidence_missing` markers when scoring an artifact. For local `skillcraft` artifacts, cite repo-relative paths such as `skills/artifact-quality-gate/SKILL.md` rather than generic descriptions.

When the artifact is a self-distillation report, receipt, continuity audit, or cross-agent handoff package, treat missing handoff inventory evidence as a targeted rework item rather than accepting a generic summary.

References: use `references/artifact_quality_validator.md` for the scoring table and `references/artifact_quality_schema.json` for machine-readable results.

## Smoke Test

Run from the `skillcraft` repository root:

```sh
python3 skills/artifact-quality-gate/scripts/validate_artifact_quality.py README.md
```

Execution mode: `real_execution` for the local command; validator mode remains `heuristic_local_validator`. If the input is a mock, fixture, dry-run, or candidate-only artifact, preserve that label in the result summary.

## Bundled Resources

- `scripts/validate_artifact_quality.py`: heuristic local validator.
- `references/artifact_quality_validator.md`: scoring rubric.
- `references/artifact_quality_schema.json`: machine-readable result schema.
