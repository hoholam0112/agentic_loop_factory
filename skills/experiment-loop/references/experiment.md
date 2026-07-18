# Stage 4: Experiment

**Purpose:** run the experiment and report the results.

## Execution

- Steps per the tech design: data preparation → training → evaluation.
- Run long jobs (anything that could outlive the session) in the background:

  ```
  mkdir -p docs/agent/loops/<loop-id>/logs
  nohup <command> > docs/agent/loops/<loop-id>/logs/<job>.log 2>&1 &
  ```

  Record in `state.json.jobs`: command, PID, log path, expected outputs.
- While a job runs, poll its log periodically. If the PID is alive but the
  log shows no progress for ~30 minutes, treat the job as stalled and
  escalate. On session resume, check whether the PID is alive and still
  matches the recorded command (PIDs get reused); if not, decide from the
  log whether it finished or failed, then continue or rerun.
- Rerun a failed job at most once. If it fails again, escalate instead of
  retrying.
- Record enough to reproduce every run: commit hash, configs, seeds, data
  versions. Keep the run config alongside its outputs.

## Report

1. Write `experiment-report.md` in the loop directory from
   `templates/experiment-report.md`, grounded in actual outputs (metrics
   files, logs). Cite the source file for every claim.
2. Verification gate (references/verification-gate.md). Criteria:
   - every number in the report traces to an output file;
   - each acceptance criterion is explicitly evaluated pass/fail;
   - limitations are noted.
   - **Self-contained:** the report is understandable on its own, without
     opening any code, config, or other document. No section defers
     understanding to an external file (e.g. "see train.py" / "refer to the
     tech design") in place of explaining. Cited files are evidence for
     claims already spelled out in the report.
   - **Sufficient depth (no placeholders):** every section holds real content,
     not template hints. Specifically: Data shows real sample rows inline and
     the split sizes; Model gives pseudo code, formulas with symbols defined,
     and every prompt verbatim; Experiment History has a filled table plus
     prose on what changed between runs; Error Analysis has ≥20 real cases
     (input / ground truth / model output) with error-pattern discussion;
     Setup shows config values inline (not just a path). Flag any section that
     is thin, generic, or still a placeholder as Major.
3. Render a self-contained HTML version into `docs/shared/` for the user,
   following `references/experiment-report-html.md`.
4. Request user review (`status: awaiting_user_review`).

**Done when:** user has reviewed the report. Set state to stage 5.
