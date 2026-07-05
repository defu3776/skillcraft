# Handoff

## Status
READY TO CONTINUE

## Original Goal
Exercise the handoff-evidence-collector smoke fixture.

## Current State
- Confirmed fixture path: `skills/handoff-evidence-collector/examples/smoke-root/project-a/handoff.md`.

## Recent Progress
- Created a minimal valid agent-continuity handoff fixture.

## Next Minimal Step
Run `handoff_inventory.py` against the fixture root.

## Next 3 Steps
1. Run the collector.
2. Inspect `Handoff_Inventory.md`.
3. Inspect `work/handoff_inventory.json`.

## Unfinished Work
### High
- None.

### Medium
- None.

### Low
- None.

## Blockers
- None.

## Do Not Retry Without New Evidence
- Do not edit source project files during this smoke.

## Files And Artifacts
- `handoff.md`: valid agent-continuity fixture.

## Decisions
- Decision: keep the fixture tiny.
  - Reason: smoke should validate classification and validator wiring, not project content.
  - Evidence: this file.

## Assumptions
- Assumption: local Python can run the sibling validator.
  - Why it is plausible: the repository bundles `skills/agent-continuity/scripts/validate_handoff.py`.
  - How to verify: run the smoke command in `SKILL.md`.

## Quick Resume
Use this fixture to confirm that the collector classifies a real `agent_continuity_handoff` and calls the configured validator path.
