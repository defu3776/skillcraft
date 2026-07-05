# Artifact Quality Validator

## Purpose

Prevent low-density agent outputs that look complete but do not answer the user's real goal.

## Scoring Table

| Dimension | Points | Checks |
|---|---:|---|
| User goal answered | 15 | Names task, goal, status, and concrete outcome. |
| Evidence bound | 20 | Uses concrete paths, repo-relative paths, commands, source dates, handoff inventory artifacts, or explicit `evidence_missing`. |
| Structure | 10 | Has skimmable sections and tables/lists where useful. |
| Input/process/output contract | 15 | States inputs, workflow, outputs, completion conditions. |
| Verification | 15 | Includes tests, smoke checks, validator, before/after comparison, score delta, or proof of execution. |
| Real/mock distinction | 10 | Distinguishes `real_execution`, `dry_run`, mock, fixture, semi-real, and `candidate_only`. |
| Repair/rollback | 10 | Includes failure repair, rollback, or next-step recovery. |
| Human entrypoint | 5 | Provides clickable receipt, index, handoff, or quick-open path. |

Thresholds:

- 90-100: ready for review or installation candidate.
- 75-89: usable, but requires targeted improvement.
- 60-74: draft only.
- below 60: not acceptable for final delivery.

## Rework Task List When Below Threshold

1. Add a one-line TASK/STATUS/OUTPUT entry.
2. Add concrete evidence paths or mark `evidence_missing`.
3. Add verification result and whether it was real, mock, dry-run, or fixture.
4. Add input/output contract.
5. Add rollback or repair path.
6. Add a human-openable entrypoint.

## Handoff-First Artifact Checks

For self-distillation, continuity audit, or cross-agent continuation artifacts, look for:

- `Handoff_Inventory.md` or `handoff_inventory.json` when the artifact claims to summarize local handoff evidence.
- Explicit source roots or an `evidence_missing` explanation when roots were unavailable.
- A clear execution label: `real_execution`, `fixture`, `mock`, `dry_run`, `semi_real`, or `candidate_only`.
- Before/after scores or validator output when the artifact claims a repair or quality improvement.
- A repair path for failed live handoffs instead of silent edits.
