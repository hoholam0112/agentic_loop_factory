# Verification Gate (shared)

Verify a stage's artifacts with a subagent before moving on. Used by stages
1, 2, 3 (per plan checkpoints), and 4 (report). Stage 5 (wiki) uses its own
prompts and flow, defined in wrap-up.md.

## Protocol

1. Dispatch ONE reviewer subagent (Agent tool, `general-purpose` type) with
   the prompt template below, filled with artifact paths and the stage's
   verification criteria (each stage reference lists its criteria).
2. Triage returned issues:
   - Issues flagged `decision_needed: yes` → escalate immediately (see
     SKILL.md), regardless of severity or round count.
   - Defects: fix them in the main session immediately.
3. Re-verify with a FRESH subagent (never reuse the previous reviewer — avoid
   anchoring). At most 3 fix-and-reverify rounds. Record the current round
   in `state.json` (e.g. `gate_round`) so the cap survives interruption;
   clear it when the gate exits.
4. Exit:
   - Only Minor issues remain → pass; note remaining Minors and proceed.
   - Major or Critical remains after 3 rounds → escalate.
5. After the gate passes, summarize to the user what this stage produced —
   in plain Korean an undergraduate can follow, jargon only when necessary
   (see SKILL.md "Plain language"). Cover what was done, what the gate found
   and how it was resolved, and any remaining Minors, before moving on.

## Rules

- Pass **file paths, not content**. The reviewer reads code and artifacts
  itself (code grounding).
- The reviewer **reports only, never modifies files**. All fixes happen in
  the main session.
- Severity is judged by **impact on experiment validity and
  reproducibility**.

## Reviewer prompt template

```
You are reviewing artifacts for an ML experiment loop. Report issues; do NOT
modify any files.

Artifacts to review:
- {artifact paths}

Verification criteria for this stage:
- {criteria from the stage reference}

Ground every finding in the actual files and code — read them directly. When
documents and code disagree, the code is the truth.

Classify each issue by impact on experiment validity and reproducibility:
- Critical: invalidates results or makes them unreproducible
- Major: meaningfully distorts results or blocks a completion criterion
- Minor: style, clarity, small inconsistencies

Return one markdown list item per issue:
- severity: Critical|Major|Minor
- location: file:line (or section)
- finding: what is wrong, with evidence
- decision_needed: yes|no — yes if this is a judgment call for the user
  rather than a defect

Return "NO ISSUES" if none found.
```
