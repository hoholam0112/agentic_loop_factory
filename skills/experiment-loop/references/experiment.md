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
- Record enough to reproduce every run: commit hash, configs, seeds, data
  versions. Keep the run config alongside its outputs.
- Store artifacts (checkpoints, prepared datasets, run outputs, plots) in the
  project's artifact location (see the artifact map). Register each significant
  artifact in `docs/agent/knowledge/artifact-map.md` when produced — path,
  type, this loop, approx size, purpose, retention (`keep`/`temp`), config
  source. This map, not git, tracks artifacts that are too large to commit.

## Monitor and continue

Don't block the session waiting on a job. Schedule a re-check on an interval;
between checks the session can do other work or idle. Each check reads the log
and the PID, decides one of the outcomes below, then re-schedules or moves on.

- Pick the interval from the job's own cadence — about the gap between the
  progress lines the code emits (see tech design / implementation), not
  shorter. A job that logs every ~10 min needs no 1-minute check.
- Use the harness's scheduled re-check mechanism (e.g. a monitor, a scheduled
  wake-up, or a recurring `/loop`) to drive it. On a plain shell, an outer
  polling loop serves the same role. Never hold the session in a blocking
  `sleep`.

Each check, decide from log + PID:

| Signal | Outcome |
|--------|---------|
| Expected outputs exist / log shows completion | **Complete** — verify outputs are non-empty and well-formed, record the run config and register artifacts, then continue to the next step. |
| PID alive, new progress line since last check | **Running** — re-schedule the next check. |
| PID alive, no new progress line for ~30 min | **Stalled** — escalate (`status: escalated`); don't kill or rerun on your own. |
| PID gone, outputs incomplete / log shows error | **Failed** — rerun once at most; if it fails again, escalate instead of retrying. |

Auto-continue only through the experiment's own steps (data preparation →
training → evaluation): when one completes, start the next without waiting for
the user. Stop for the user only at the report-review gate below, and at any
escalation.

**On session resume** (fresh session, jobs still in `state.json.jobs`): for
each job, check whether the PID is alive and still matches the recorded command
(PIDs get reused). If alive, resume monitoring. If gone, decide from the log
whether it finished or failed, then continue or rerun per the table above.
Update `state.json` (`stage`, `current_task`, `jobs`) after every transition so
the next check — or the next session — starts from the truth.

## Report

1. Write `experiment-report.md` in the loop directory from
   `templates/experiment-report.md`, grounded in actual outputs (metrics
   files, logs). Cite the source file for every claim. Two passes:
   1. **Fill the floor.** Complete every mandatory section of the template to
      the required depth — this is the non-negotiable baseline, not the goal.
   2. **Extend for the reader.** Then stop and ask: *what would help the user
      understand THIS experiment better?* Add whatever the standard sections
      miss — an extra comparison, a breakdown by slice, a failure taxonomy, a
      chart that makes a result obvious at a glance, a "what surprised us" note.
      The template is a floor, not a cap; adding beyond it is expected. Briefly
      say why each addition helps (one line) so the choice is deliberate, not
      decoration. Don't invent data — additions must come from the same outputs.
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
3. Render a self-contained HTML version into `docs/shared/<loop-id>-report.html`
   for the user: copy `templates/experiment-report.html`, fill in every `FILL`
   marker, and follow `references/experiment-report-html.md`. Then run its
   verification gate — open the rendered file in a browser and confirm it loads,
   every tab shows real content, and every chart draws with real data before
   handing it over.
4. Request user review (`status: awaiting_user_review`).

**Done when:** user has reviewed the report. Set state to stage 5.
