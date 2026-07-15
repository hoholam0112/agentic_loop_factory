# experiment-loop Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the `experiment-loop` Claude Code skill (SKILL.md + 8 references + 6 templates) in `skills/experiment-loop/`, per `docs/superpowers/specs/2026-07-15-experiment-loop-skill-design.md`.

**Architecture:** One thin SKILL.md acting as a dashboard and loop controller; per-stage detail in `references/` loaded on stage entry; document skeletons in `templates/`. All deliverables are markdown — "tests" are mechanical checks (English-only, file existence, SKILL.md line budget) plus a final traceability review against the source requirements.

**Tech Stack:** Markdown only. No code, no build.

## Global Constraints

- Every file is in **English**. Check: `grep -n '[가-힣]' <file>` must return nothing (exit code 1).
- All files live under `skills/experiment-loop/`.
- **Self-contained:** no file may reference superpowers, plugins, or any external skill. Subagents are invoked only via the built-in Agent tool, `general-purpose` type.
- SKILL.md stays thin: **≤ 120 lines**. Stage detail belongs in references.
- References state purpose / deliverables / done-when and keep procedure prescriptions minimal ("what/why over how").
- Spec: `docs/superpowers/specs/2026-07-15-experiment-loop-skill-design.md`. Source requirements: `prompts/build-experiment-loop.md` (Korean — for the final traceability check only).
- Commit after every task.

---

### Task 1: SKILL.md

**Files:**
- Create: `skills/experiment-loop/SKILL.md`

**Interfaces:**
- Produces: the stage table (stage → reference path → done-when) and the `state.json` schema that every reference file relies on. Reference filenames used here must match Tasks 2–8 exactly: `verification-gate.md`, `bootstrap.md`, `long-term-plan.md`, `experiment-design.md`, `tech-design.md`, `implementation.md`, `experiment.md`, `wrap-up.md`.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
---
name: experiment-loop
description: Use when working on ML experiments in this project - starting or resuming an experiment loop, onboarding the project (bootstrap), designing experiments, tech design, implementation, running experiments, or wrapping up a loop. Acts as a dashboard for loop state.
---

# Experiment Loop

Agentic loop for ML experiment automation. The main session controls the
loop; verification runs in subagents. This file stays thin: stage details
live in `references/` and are loaded only when entering a stage.

## On Invocation: Dashboard

1. Check onboarding: if `docs/index.md` does not exist, the project is not
   onboarded. Explain stage 0 and load `references/bootstrap.md`.
2. Find the latest loop state: `docs/loops/*/state.json` (directories sort
   chronologically by name). If none exists, offer to start the first loop
   (stage 1).
3. If state exists, summarize `loop_id`, `stage`, `status`, `current_task`,
   plus any `pending_decisions` and running `jobs`. Offer:
   (a) continue from the recorded point, (b) start a new loop,
   (c) re-enter a specific stage.
4. Load the reference for the chosen stage and proceed.

## Stages

| Stage | Reference | Done when |
|-------|-----------|-----------|
| 0 bootstrap (once per project) | references/bootstrap.md | Layout + index.md + PRD exist; user confirms |
| 0.5 long-term plan (as needed) | references/long-term-plan.md | User approves multi-loop plan |
| 1 experiment design | references/experiment-design.md | User approves Experiment Design Doc |
| 2 tech design | references/tech-design.md | User approves Tech Design Spec + Implementation Plan |
| 3 implementation | references/implementation.md | All tasks done, tests pass, gate passed |
| 4 experiment | references/experiment.md | User reviewed Experiment Report |
| 5 wrap-up | references/wrap-up.md | GC done, wiki verified, state closed |

Stages run 1→5 within a loop. Enter 0.5 from stage 1 when the user's
requirement doesn't fit one loop; if unsure, ask the user.

## Shared Principles (all stages)

- **Code grounding.** Docs are for fast context only. Read the actual code
  before judging, implementing, or verifying. When docs and code disagree,
  trust the code.
- **Progressive context loading.** Don't load everything up front. On user
  input: read `docs/glossary.md` and align terminology (ask when a user's
  term is ambiguous), then read `docs/index.md` and open only the documents
  the task needs.
- **What/why over how.** Each stage defines purpose, deliverables, and
  completion criteria. Choose the implementation approach yourself.
- **Escalation.** Stop the loop, record the question in
  `state.json.pending_decisions`, describe the situation to the user, and
  present 2-3 options.
- **Verification gates** run in subagents per
  `references/verification-gate.md`.
- **Language.** All docs and code in English. `docs/glossary.md` may contain
  Korean. Communicate with the user in Korean (technical terms in English).

## Project Layout

Default layout. Bootstrap adapts it to existing project conventions and
records the result in `docs/index.md`; later stages follow `index.md`, not
this default.

```
docs/
  index.md            # navigation for agents (agent-maintained)
  glossary.md         # human<->agent terminology
  human/              # human-authored (PRD, ...)
  wiki/               # agent-generated, project-persistent
  loops/<loop-id>/    # loop-scoped: design doc, spec, plan, report, state.json
```

`loop-id` = zero-padded sequence + slug, e.g. `003-lora-rank-sweep`.

## state.json

`docs/loops/<loop-id>/state.json` — the minimum for resume-after-interruption
and handoff to the next loop. Update on every stage transition, escalation,
and job start.

```json
{
  "loop_id": "003-lora-rank-sweep",
  "stage": 3,
  "status": "in_progress",
  "current_task": "eval harness (task 4/6)",
  "artifacts": {"experiment_design": "docs/loops/003-lora-rank-sweep/experiment-design.md"},
  "jobs": [{"command": "python train.py ...", "pid": 12345, "log": "docs/loops/003-lora-rank-sweep/logs/train.log", "outputs": "runs/003/"}],
  "pending_decisions": [],
  "handoff_notes": null
}
```

`status`: `in_progress` | `awaiting_user_review` | `escalated` | `done`.
Extend fields as needed; keep these as the minimum.

## Templates

Use `templates/` when creating documents: `prd.md`,
`experiment-design-doc.md`, `tech-design-spec.md`, `task-spec.md`,
`implementation-plan.md`, `experiment-report.md`.
````

- [ ] **Step 2: Verify**

Run: `grep -n '[가-힣]' skills/experiment-loop/SKILL.md; echo "exit=$?"`
Expected: no matches, `exit=1`

Run: `wc -l skills/experiment-loop/SKILL.md`
Expected: ≤ 120

- [ ] **Step 3: Commit**

```bash
git add skills/experiment-loop/SKILL.md
git commit -m "feat(experiment-loop): add SKILL.md core (dashboard, stages, state)"
```

---

### Task 2: verification-gate.md

**Files:**
- Create: `skills/experiment-loop/references/verification-gate.md`

**Interfaces:**
- Consumes: escalation rule and severity vocabulary from SKILL.md (Task 1).
- Produces: the gate protocol and reviewer prompt template that stage references (Tasks 4–8) invoke by the phrase "verification gate (references/verification-gate.md)".

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Verification Gate (shared)

Verify a stage's artifacts with a subagent before moving on. Used by stages
1, 2, 3 (per plan checkpoints), 4 (report), and 5 (wiki, which has its own
prompts in wrap-up.md but follows this protocol).

## Protocol

1. Dispatch ONE reviewer subagent (Agent tool, `general-purpose` type) with
   the prompt template below, filled with artifact paths and the stage's
   verification criteria (each stage reference lists its criteria).
2. Triage returned issues:
   - Issues flagged `decision_needed: yes` → escalate immediately (see
     SKILL.md), regardless of severity or round count.
   - Defects: fix them in the main session immediately.
3. Re-verify with a FRESH subagent (never reuse the previous reviewer — avoid
   anchoring). At most 3 fix-and-reverify rounds.
4. Exit:
   - Only Minor issues remain → pass; note remaining Minors and proceed.
   - Major or Critical remains after 3 rounds → escalate.

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
````

- [ ] **Step 2: Verify**

Run: `grep -n '[가-힣]' skills/experiment-loop/references/verification-gate.md; echo "exit=$?"`
Expected: no matches, `exit=1`

- [ ] **Step 3: Commit**

```bash
git add skills/experiment-loop/references/verification-gate.md
git commit -m "feat(experiment-loop): add shared verification gate protocol"
```

---

### Task 3: bootstrap.md + long-term-plan.md

**Files:**
- Create: `skills/experiment-loop/references/bootstrap.md`
- Create: `skills/experiment-loop/references/long-term-plan.md`

**Interfaces:**
- Consumes: Project Layout and dashboard rules from SKILL.md (Task 1); `templates/prd.md` (Task 9).
- Produces: `docs/index.md`, `docs/glossary.md` conventions consumed by all later stages; `docs/wiki/long-term-plan.md` consumed by stage 1 and stage 5.

- [ ] **Step 1: Write `references/bootstrap.md` with exactly this content**

````markdown
# Stage 0: Bootstrap (once per project)

Onboard a project onto the experiment loop. Run only when `docs/index.md`
does not exist.

**Purpose:** understand the project and restructure its documentation so the
loop can run on it.

## Deliverables

- Doc layout per SKILL.md "Project Layout", adapted to the project's existing
  conventions.
- `docs/glossary.md` — terminology used in the project and by the user
  (the one document where Korean is allowed).
- `docs/index.md` — what every document is for and when an agent should read
  it, including the layout decisions made here. Later stages navigate by
  this file.
- A PRD in `docs/human/`. If none exists, guide the user through writing one
  using `templates/prd.md`: interview them section by section — the user
  authors the content, you scribe.

## Procedure outline (adapt as needed)

1. Analyze the project: README, existing docs, code structure,
   experiment/training entry points, test setup. Read the code, not just the
   docs.
2. Propose the layout adaptation to the user (what moves where, what gets
   created). Get agreement before moving any files.
3. Restructure. Classify existing docs on two axes: human-authored vs
   agent-generated, loop-scoped vs project-persistent. Write `glossary.md`
   and `index.md`.
4. Ensure the PRD exists.

**Done when:** layout exists, `index.md` reflects it, PRD present, and the
user confirms. Then start the first loop (stage 1).
````

- [ ] **Step 2: Write `references/long-term-plan.md` with exactly this content**

````markdown
# Stage 0.5: Long-Term Plan

Entered from stage 1 when the user's requirement doesn't fit a single loop.
If unsure whether it fits, ask the user.

**Purpose:** break a multi-loop requirement into a sequence of loop-sized
goals.

## Deliverables

- `docs/wiki/long-term-plan.md` (project-persistent): an ordered list of
  loop-sized goals, each with — goal, why it needs its own loop,
  dependencies, rough success criterion. Mark which goal the next loop
  takes.

## Procedure outline

1. Clarify the overall requirement with the user.
2. Decompose into loop-sized goals; present the plan; revise until approved.
3. Maintain the document at every loop's wrap-up: mark finished goals done,
   adjust the remainder.

**Done when:** user approves the plan. Return to stage 1 with the first
unfinished goal as this loop's scope.
````

- [ ] **Step 3: Verify**

Run: `grep -rn '[가-힣]' skills/experiment-loop/references/bootstrap.md skills/experiment-loop/references/long-term-plan.md; echo "exit=$?"`
Expected: no matches, `exit=1`

- [ ] **Step 4: Commit**

```bash
git add skills/experiment-loop/references/bootstrap.md skills/experiment-loop/references/long-term-plan.md
git commit -m "feat(experiment-loop): add bootstrap and long-term-plan references"
```

---

### Task 4: experiment-design.md

**Files:**
- Create: `skills/experiment-loop/references/experiment-design.md`

**Interfaces:**
- Consumes: state.json schema (Task 1), verification gate (Task 2), `templates/experiment-design-doc.md` (Task 9).
- Produces: `docs/loops/<loop-id>/experiment-design.md` consumed by stage 2.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Stage 1: Experiment Design

**Purpose:** decide what this loop experiments on, and specify it well enough
that tech design can start.

## Deliverables

- New loop directory `docs/loops/<loop-id>/` (`loop-id` = next zero-padded
  sequence + slug) with `state.json` initialized: stage 1, `in_progress`.
- `experiment-design.md` in the loop directory, from
  `templates/experiment-design-doc.md`: problem definition / data /
  hypothesis / search space / evaluation method / acceptance criteria /
  out of scope.

## Procedure outline

1. Load context progressively (glossary → index → needed docs). Read the
   relevant code to ground the current state.
2. Research recent papers, repositories, and blog posts relevant to the
   problem (web search). Capture findings with sources.
3. Propose 2-3 candidate directions for this loop with trade-offs and a
   recommendation. Always include a free-form option for the user's own
   idea. If the chosen direction doesn't fit one loop, enter stage 0.5.
4. Write the design doc. For every open decision, present the user 2-3
   alternatives or ask directly.
5. Verification gate (references/verification-gate.md). Criteria:
   - the hypothesis is falsifiable;
   - the evaluation method can actually measure the acceptance criteria;
   - the data section matches the real data (checked against code/files);
   - the scope is achievable in one loop.
6. Request user review (`status: awaiting_user_review`); revise until
   approved.

**Done when:** user approves the doc. Set state to stage 2.
````

- [ ] **Step 2: Verify**

Run: `grep -n '[가-힣]' skills/experiment-loop/references/experiment-design.md; echo "exit=$?"`
Expected: no matches, `exit=1`

- [ ] **Step 3: Commit**

```bash
git add skills/experiment-loop/references/experiment-design.md
git commit -m "feat(experiment-loop): add experiment-design reference"
```

---

### Task 5: tech-design.md

**Files:**
- Create: `skills/experiment-loop/references/tech-design.md`

**Interfaces:**
- Consumes: stage-1 design doc path convention (Task 4), verification gate (Task 2), `templates/tech-design-spec.md`, `templates/implementation-plan.md`, `templates/task-spec.md` (Task 9).
- Produces: `docs/loops/<loop-id>/tech-design-spec.md` and `implementation-plan.md` consumed by stage 3.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Stage 2: Tech Design

**Purpose:** design the code changes needed to run the experiment, grounded
in the actual codebase.

**Code grounding is mandatory here:** read every module the design touches
before writing AS-IS. The AS-IS section must cite real files.

## Deliverables (both in the loop directory)

- `tech-design-spec.md` from `templates/tech-design-spec.md`:
  - AS-IS / TO-BE, based on the Experiment Design Doc;
  - acceptance criteria restated technically — the exact command, metric,
    and threshold that decides each one;
  - verification plan: how the code will be reviewed and tested.
- `implementation-plan.md` from `templates/implementation-plan.md`:
  - tasks (each specified per `templates/task-spec.md`), their order and
    dependencies, and verification checkpoints — covering implementation
    through verification.

## Procedure outline

1. Write the spec, then the plan.
2. Verification gate (references/verification-gate.md). Criteria:
   - AS-IS matches the real code;
   - TO-BE is sufficient to run the designed experiment;
   - every acceptance criterion has a technical verification;
   - tasks are ordered, dependency-correct, and individually testable.
3. Request user review; revise until approved.

**Done when:** user approves both documents. Set state to stage 3.
````

- [ ] **Step 2: Verify**

Run: `grep -n '[가-힣]' skills/experiment-loop/references/tech-design.md; echo "exit=$?"`
Expected: no matches, `exit=1`

- [ ] **Step 3: Commit**

```bash
git add skills/experiment-loop/references/tech-design.md
git commit -m "feat(experiment-loop): add tech-design reference"
```

---

### Task 6: implementation.md

**Files:**
- Create: `skills/experiment-loop/references/implementation.md`

**Interfaces:**
- Consumes: implementation plan produced per Task 5's conventions; verification gate (Task 2).
- Produces: implemented, tested code — stage 4 assumes the experiment can run.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Stage 3: Implementation

**Purpose:** implement the plan's tasks so the experiment can run.

## Method: TDD, task by task, in plan order

For each task in `implementation-plan.md`:

1. Write a failing test that captures the task's done-condition. Run it and
   confirm it fails for the expected reason.
2. Write the minimal implementation to make it pass. Run the test; confirm
   it passes.
3. Refactor if needed; keep the whole suite green.
4. Commit, referencing the task.
5. Update `state.json.current_task`.

Run tests in the main session — the TDD cycle needs immediate feedback. Do
not delegate the cycle to subagents.

## Verification gate

Run the gate (references/verification-gate.md) at each checkpoint the
implementation plan marks — at minimum once after all tasks. Criteria:

- the code matches the Tech Design Spec;
- tests actually verify the acceptance criteria rather than restating the
  implementation;
- no unexplained deviations from the plan.

**Done when:** all tasks complete, the full test suite passes, and the gate
passed. Set state to stage 4.
````

- [ ] **Step 2: Verify**

Run: `grep -n '[가-힣]' skills/experiment-loop/references/implementation.md; echo "exit=$?"`
Expected: no matches, `exit=1`

- [ ] **Step 3: Commit**

```bash
git add skills/experiment-loop/references/implementation.md
git commit -m "feat(experiment-loop): add implementation reference"
```

---

### Task 7: experiment.md

**Files:**
- Create: `skills/experiment-loop/references/experiment.md`

**Interfaces:**
- Consumes: `state.json.jobs` schema (Task 1), verification gate (Task 2), `templates/experiment-report.md` (Task 9).
- Produces: `docs/loops/<loop-id>/experiment-report.md` (+ `.html`) consumed by stage 5 and the user.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
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
````

- [ ] **Step 2: Verify**

Run: `grep -n '[가-힣]' skills/experiment-loop/references/experiment.md; echo "exit=$?"`
Expected: no matches, `exit=1`

- [ ] **Step 3: Commit**

```bash
git add skills/experiment-loop/references/experiment.md
git commit -m "feat(experiment-loop): add experiment reference"
```

---

### Task 8: wrap-up.md

**Files:**
- Create: `skills/experiment-loop/references/wrap-up.md`

**Interfaces:**
- Consumes: `handoff_notes`/`status` fields (Task 1), gate protocol (Task 2), `docs/wiki/long-term-plan.md` convention (Task 3).
- Produces: closed loop state; updated wiki; the two wiki subagent prompt templates.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Stage 5: Wrap-Up

**Purpose:** leave the project clean and the knowledge base current; close
the loop.

## 1. Garbage collection

Identify code, configs, and docs made obsolete by this loop (superseded
experiments, dead flags, scratch scripts). Confirm with the user before
deleting anything this loop did not create. Commit deletions separately.

## 2. LLM wiki update

1. Compute the change scope: `git diff --name-only <loop-start>..HEAD` plus
   changes to human-authored docs.
2. Dispatch one updater subagent per `docs/wiki/` subdirectory in parallel
   (Agent tool, `general-purpose` type) with the updater prompt below.
   Updaters write directly — directories are disjoint, so no conflicts.
3. Dispatch one verifier subagent per updated directory with the verifier
   prompt below. The main session fixes only failed claims.
4. Update `docs/index.md` and `docs/glossary.md` if structure or terminology
   changed.

### Updater prompt template

```
You maintain the agent-generated wiki of this project. Update the documents
in {directory} to reflect this loop's changes. Changed files this loop:
{changed file list}

Rules:
- Work claim by claim: check each existing claim against the current code;
  fix stale claims, delete obsolete ones, add claims for new behavior.
- Every claim cites its source file (and line where useful).
- Only edit files inside {directory}. English only.

Return: the files you changed, with a one-line summary each.
```

### Verifier prompt template

```
Verify agent-maintained wiki documents in {directory}. For each claim
modified in this update ({changed docs}), read the cited source and confirm
the claim is accurate. Do NOT modify any files.

Return per claim: file, claim, verdict (accurate | inaccurate |
unverifiable), evidence. Return "ALL ACCURATE" if everything checks out.
```

## 3. Tools & hooks review

Review this loop's friction: commands typed repeatedly, mistakes a hook
could have caught. Add a tool or hook ONLY if the need occurred at least
twice this loop. Avoid overbuilding — none is a fine outcome.

## 4. Close the loop

- Write `state.json.handoff_notes`: what the next loop should know —
  unfinished threads, surprises, recommended next goal.
- Update `docs/wiki/long-term-plan.md` if it exists.
- Set `status: "done"`.

**Done when:** all four sections are complete.
````

- [ ] **Step 2: Verify**

Run: `grep -n '[가-힣]' skills/experiment-loop/references/wrap-up.md; echo "exit=$?"`
Expected: no matches, `exit=1`

- [ ] **Step 3: Commit**

```bash
git add skills/experiment-loop/references/wrap-up.md
git commit -m "feat(experiment-loop): add wrap-up reference with wiki subagent prompts"
```

---

### Task 9: Templates (6 files)

**Files:**
- Create: `skills/experiment-loop/templates/prd.md`
- Create: `skills/experiment-loop/templates/experiment-design-doc.md`
- Create: `skills/experiment-loop/templates/tech-design-spec.md`
- Create: `skills/experiment-loop/templates/task-spec.md`
- Create: `skills/experiment-loop/templates/implementation-plan.md`
- Create: `skills/experiment-loop/templates/experiment-report.md`

**Interfaces:**
- Consumes: nothing.
- Produces: the section skeletons that references (Tasks 3–7) name. Section headings here are the contract — they must match what the references list.

- [ ] **Step 1: Write `templates/prd.md`**

```markdown
# PRD: <project name>

## Background
<!-- Why this project exists; the current situation -->

## Problem
<!-- The specific problem to solve -->

## Goals
<!-- Measurable outcomes -->

## Non-Goals
<!-- Explicitly out of scope -->

## Success Metrics
<!-- How success is measured -->

## Constraints
<!-- Data, compute, deadlines, dependencies -->
```

- [ ] **Step 2: Write `templates/experiment-design-doc.md`**

```markdown
# Experiment Design: <loop-id>

## Problem Definition
<!-- What question this loop answers -->

## Data
<!-- Datasets, versions, splits — verified against actual files -->

## Hypothesis
<!-- A falsifiable statement -->

## Search Space
<!-- What is varied: models, hyperparameters, features -->

## Evaluation Method
<!-- Metrics, baselines, protocol -->

## Acceptance Criteria
<!-- Conditions that make this loop a success -->

## Out of Scope
<!-- Explicitly excluded from this loop -->
```

- [ ] **Step 3: Write `templates/tech-design-spec.md`**

```markdown
# Tech Design Spec: <loop-id>

## Summary
<!-- One paragraph: what changes and why -->

## AS-IS
<!-- Current code relevant to this experiment, with file citations -->

## TO-BE
<!-- Target design; what is added or changed -->

## Acceptance Criteria (Technical)
<!-- Each criterion: the exact command, metric, and threshold that decides it -->

## Verification Plan
<!-- Code review focus areas; test approach -->
```

- [ ] **Step 4: Write `templates/task-spec.md`**

```markdown
# Task <N>: <name>

## Goal
<!-- What this task delivers, one sentence -->

## Files
<!-- Create/modify, exact paths -->

## Interfaces
<!-- Consumes / produces: exact names and signatures -->

## Test Plan
<!-- What the failing test asserts first -->

## Done When
<!-- Independently checkable condition -->
```

- [ ] **Step 5: Write `templates/implementation-plan.md`**

```markdown
# Implementation Plan: <loop-id>

## Overview
<!-- Order of work and why -->

## Tasks
<!-- Ordered list; each follows task-spec.md; note dependencies -->

## Verification Checkpoints
<!-- Where verification gates run during stage 3 -->
```

- [ ] **Step 6: Write `templates/experiment-report.md`**

```markdown
# Experiment Report: <loop-id>

## Summary
<!-- One paragraph: what was tested, what happened, verdict -->

## Setup
<!-- Commit hash, configs, data versions, seeds -->

## Results
<!-- Tables/plots; every number cites its source file -->

## Analysis
<!-- Interpretation; surprises; error analysis -->

## Acceptance Criteria Check
<!-- Each criterion: pass/fail with evidence -->

## Conclusions & Next Steps
<!-- What this means; recommended follow-up -->
```

- [ ] **Step 7: Verify**

Run: `grep -rn '[가-힣]' skills/experiment-loop/templates/; echo "exit=$?"`
Expected: no matches, `exit=1`

Run: `ls skills/experiment-loop/templates/ | sort`
Expected: exactly `experiment-design-doc.md experiment-report.md implementation-plan.md prd.md task-spec.md tech-design-spec.md`

- [ ] **Step 8: Commit**

```bash
git add skills/experiment-loop/templates/
git commit -m "feat(experiment-loop): add six document templates"
```

---

### Task 10: Traceability review and fixes

**Files:**
- Modify: any file under `skills/experiment-loop/` where a gap is found.

**Interfaces:**
- Consumes: everything from Tasks 1–9; `prompts/build-experiment-loop.md`; the design spec.

- [ ] **Step 1: Traceability check**

Read `prompts/build-experiment-loop.md` section by section. For each requirement, name the file (and section) in `skills/experiment-loop/` that implements it. Requirements to check explicitly:

- single skill, thin SKILL.md, references loaded per stage
- state.json for resume + handoff; dashboard behavior on invocation
- progressive context loading via glossary.md then index.md; ask on ambiguous terms
- verification gate: subagent, Critical/Major/Minor by validity+reproducibility impact, immediate fix, 3 rounds max, Minor-only skip, Major+ escalation, decision issues escalate with 2-3 options, loop stops on escalation
- all 7 stages (0, 0.5, 1, 2, 3, 4, 5) with their stated deliverables and user-review gates
- LLM wiki: two-axis doc classification, glossary/index navigation docs, per-claim source citation, parallel per-directory subagent update+verify
- templates for all six document types
- English-only rule with glossary exception; Korean for user communication

- [ ] **Step 2: Fix any gaps found**

Edit the affected file(s) directly. If a requirement has no home, add it to the most local file (a stage reference over SKILL.md).

- [ ] **Step 3: Final structural check**

Run: `wc -l skills/experiment-loop/SKILL.md`
Expected: still ≤ 120

Run: `grep -rn -i 'superpowers\|plugin' skills/experiment-loop/; echo "exit=$?"`
Expected: no matches, `exit=1` (self-containment)

Run: `find skills/experiment-loop -type f | sort`
Expected: 15 files — SKILL.md, 8 references, 6 templates

- [ ] **Step 4: Commit (only if fixes were made)**

```bash
git add skills/experiment-loop/
git commit -m "fix(experiment-loop): close traceability gaps against source requirements"
```
