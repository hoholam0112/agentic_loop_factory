# Experiment Report: <loop-id>

<!--
Writing principles:
- Detailed but easy, undergraduate-readable. Explain, don't just list.
- SELF-CONTAINED. A reader must understand the whole experiment from THIS
  report alone, without opening any code, config, or other document. Do not
  write "see the tech design" or "refer to train.py" as a substitute for
  explaining. When you cite a source file, it is evidence for a claim you have
  already spelled out here — not a pointer the reader must follow to understand.
  Restate the necessary context (what the data is, what the model does, what
  each metric means) inside the report.
- Every number cites its source output file (evidence, not a stand-in for
  explanation).
- No placeholders. Every section holds real content; if something is genuinely
  N/A, say so and why.

This markdown feeds the HTML report (references/experiment-report-html.md); the
sections below map to its five tabs, plus setup and acceptance-criteria for the
verification gate.
-->

## Overview
<!-- Background: why this experiment exists. Problem definition in plain words.
     Data summary, experiment-history summary, headline accuracy and
     performance metrics. A reader should grasp the whole story from this
     section alone, then get the depth in later sections. -->

## Data
<!-- Collection: where the data came from and how. Preprocessing: every step,
     in order. Composition: sizes of train/val/test splits, class balance,
     key statistics. Sample data: at least a few real rows/examples shown
     inline so the reader sees what the data actually looks like. Define any
     dataset-specific terms. -->

## Model
<!-- For each model/approach tried: what it does, explained from scratch.
     Include pseudo code AND the key formulas (with each symbol defined).
     Include every prompt VERBATIM (original text, in a code block). A reader
     who has never seen the codebase should be able to understand the method
     from this section without opening any source file. -->

## Experiment History
<!-- A table of every experiment run: config/variant, key result, and a short
     takeaway per row. Follow the table with prose explaining what changed
     between runs and why, so the progression is a narrative, not just numbers. -->

## Error Analysis
<!-- At least 20-30 sampled cases, mixing wrong and correct. For each: input
     data, ground-truth answer, model output, side by side. Group by error
     type and explain the patterns you see — what fails, and a hypothesis for
     why. The samples are shown inline (not linked out). -->

## Setup / Reproducibility
<!-- Commit hash, configs (values shown inline, not just a path), data
     versions, seeds, hardware/runtime. Enough that someone could reproduce
     the run from this section alone. -->

## Acceptance Criteria Check
<!-- Each acceptance criterion restated, then pass/fail with the concrete
     evidence (numbers + source file). -->

## Conclusions & Next Steps
<!-- What the results mean in plain words, whether the hypothesis held, and
     recommended follow-up. -->
