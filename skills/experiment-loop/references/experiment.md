# Stage 4: Experiment

**Purpose:** run the experiment and report the results.

## Execution

- Steps per the tech design: data preparation → training → evaluation.
- Run long jobs (anything that could outlive the session) in the background:

  ```
  nohup <command> > docs/loops/<loop-id>/logs/<job>.log 2>&1 &
  ```

  Record in `state.json.jobs`: command, PID, log path, expected outputs.
- While a job runs, poll its log periodically. On session resume, check
  whether the PID is alive; if not, decide from the log whether it finished
  or failed, then continue or rerun.
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
3. Render a self-contained HTML version next to the markdown for
   readability.
4. Request user review (`status: awaiting_user_review`).

**Done when:** user has reviewed the report. Set state to stage 5.
