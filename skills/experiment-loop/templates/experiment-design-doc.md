# Experiment Design: <loop-id>

<!--
Writing principle: detailed but easy. Avoid hard words; use jargon only when
necessary. An undergraduate should be able to read this without effort.
-->

## Problem Definition
<!-- What question this loop answers -->

## Data
<!-- Datasets, versions, splits — verified against actual files -->

## External Research
<!-- Recent papers and open-source repos. Put full findings in a separate
     file; keep only a summary and references here -->

## Prior Error Review
<!-- Look back at the errors from previous loops before designing this one.
     Read the last loop's experiment report (its Error Analysis section) and
     any relevant knowledge/decision notes. Summarize the main error patterns
     found, and state which of them this loop tries to address (and which it
     deliberately leaves out). Cite the source report/section for each claim.
     If this is the first loop (no prior report), say so. -->

## Hypothesis
<!-- A falsifiable statement. Where relevant, tie it back to the prior errors
     above -->

## Search Space
<!-- The decisions behind what is varied, not just a final list. For each factor
     (models, hyperparameters, features): the candidate values/range and why,
     plus the run budget (how many runs, how searched). Mark which high-impact
     choices the user picked from offered options; for any value filled by a
     recommended default, give the reason. No material factor may be fixed
     without having offered the user the choice first. -->

## Evaluation Method
<!-- Metrics, baselines, protocol -->

## Constraints
<!-- Latency, throughput, cost, legal issues, etc. -->

## Acceptance Criteria
<!-- Conditions that make this loop a success (accuracy, completeness, ...) -->

## Out of Scope
<!-- Explicitly excluded from this loop -->
