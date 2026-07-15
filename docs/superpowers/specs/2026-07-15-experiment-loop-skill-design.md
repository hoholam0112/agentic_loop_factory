# experiment-loop Skill — Design Spec

**Date:** 2026-07-15
**Source requirements:** `prompts/build-experiment-loop.md`
**Status:** Approved design, pending implementation

## Purpose

A single Claude Code skill (`experiment-loop`) that runs an agentic loop for ML
experiment automation: from project onboarding through experiment design, tech
design, implementation, experiment execution, and wrap-up. The skill tells the
agent **what and why**; **how** is delegated to the agent's judgment. Every
stage defines purpose, deliverables, and completion criteria.

This repo (`agentic_loop_factory`) is the source of truth for the skill.
Deployment to a target ML project is done by copying or symlinking
`skills/experiment-loop/` into that project's `.claude/skills/`.

## Decisions Made

| Decision | Choice |
|---|---|
| Skill location | Source-managed in this repo at `skills/experiment-loop/` |
| Dependencies | Fully self-contained; no references to superpowers or other plugins |
| Target project doc layout | Skill defines a default layout; stage 0 bootstrap adapts it to existing project conventions and records the result in `index.md` |
| Long-running jobs (stage 4) | Background execution (`nohup`-style) with command, PID, and log path recorded in `state.json` for resume |
| Reference granularity | One reference file per stage + one shared verification-gate reference |
| Language | All skill files, docs, and code in English; `glossary.md` is the only Korean-permitted document; user communication in Korean |

## Skill Directory Structure

```
skills/experiment-loop/
  SKILL.md                      # thin: dashboard, loop control, stage table, shared principles
  references/
    bootstrap.md                # stage 0: doc restructuring, PRD guidance, onboarding (run once)
    long-term-plan.md           # stage 0.5: multi-loop long-term planning
    experiment-design.md        # stage 1: research, option proposals, Experiment Design Doc
    tech-design.md              # stage 2: Tech Design Spec + Implementation Plan
    implementation.md           # stage 3: TDD implementation (self-contained description)
    experiment.md               # stage 4: background job execution + report (md then html)
    wrap-up.md                  # stage 5: garbage collection, wiki update, tools/hooks review
    verification-gate.md        # shared: subagent verification protocol
  templates/
    prd.md
    experiment-design-doc.md
    tech-design-spec.md
    task-spec.md
    implementation-plan.md
    experiment-report.md
```

## SKILL.md Behavior (thin core)

1. **Dashboard on invocation.** Find the latest loop's `state.json` under
   `docs/loops/`. Summarize current stage and status, then offer options:
   continue from the recorded point / start a new loop / re-enter a specific
   stage. If no state exists (project not onboarded), guide the user to stage 0
   bootstrap.
2. **Stage table.** One row per stage: stage → reference file → one-line
   completion criterion. The reference for a stage is loaded only when
   entering that stage (progressive disclosure).
3. **Shared principles**, stated once in SKILL.md and applying to all stages:
   - **Code grounding.** Docs are for fast context only; read actual code
     before judging, implementing, or verifying. When docs and code disagree,
     trust the code.
   - **Progressive context loading.** On user input, read `glossary.md` to
     align terminology (ask when a user's term is ambiguous), then read
     `index.md` to select and load only the documents needed.
   - **Escalation rule.** Stop the loop, describe the situation, present 2–3
     options to the user.
   - **Language rule** as decided above.
4. **Main session controls the loop; verification runs in subagents** to avoid
   context contamination.

## Target Project Default Layout

Created/adapted by bootstrap; the final structure is recorded in `index.md`
and later stages follow `index.md`, not this default.

```
docs/
  index.md            # navigation for agents (agent-maintained)
  glossary.md         # human↔agent terminology (only Korean-permitted doc)
  human/              # human-authored docs (PRD, etc.)
  wiki/               # agent-generated, project-persistent (LLM wiki)
  loops/<loop-id>/    # loop-scoped artifacts: design doc, spec, plan, report, state.json
```

- `loop-id` format: `NNN-slug` (zero-padded sequence + short slug), e.g.
  `003-lora-rank-sweep`, so directory listing sorts chronologically.
- Agent-generated wiki documents cite file sources per claim.

## state.json

Located at `docs/loops/<loop-id>/state.json`. Goal: the minimum needed for
**resume after interruption** and **handoff to the next loop**. Fields:

- `loop_id`
- `stage` (0, 0.5, 1–5)
- `start_commit`: the commit the loop started from, recorded at stage 1,
  used by wrap-up's change-scope diff
- `status`: `in_progress` | `awaiting_user_review` | `escalated` | `done`
- `current_task`: short description of what is underway
- `artifacts`: paths of stage deliverables produced so far
- `jobs`: background jobs — command, PID, log path, expected outputs
- `pending_decisions`: escalated questions with their options
- `handoff_notes`: notes for the next loop (written during wrap-up)

Updated at every stage transition, escalation, and job start. The agent may
extend fields as needed; these are the required minimum.

## Subagent Invocation Model

The skill is self-contained, so all subagents use the built-in Agent tool with
the `general-purpose` type — no custom agent definitions to deploy. Each
subagent role has a **prompt template inside the relevant reference file**
(`verification-gate.md`, `wrap-up.md`); the main session fills placeholders
(artifact paths, stage criteria, changed-file lists) and dispatches.

Shared rules:

- Pass **file paths, not content**. Subagents read code themselves, enforcing
  code grounding.
- Subagents return a **structured issue list**: severity, `file:line`,
  rationale, and a flag for decision-type issues. The main session classifies,
  fixes, or escalates based on it.
- Reviewer subagents **report only, never modify**. Fixes happen in the main
  session — loop control and fix context stay in the main session; the bulk
  file-reading of review is isolated in the subagent. Exception: wiki-update
  subagents write docs directly (see Wrap-up), since directories are disjoint
  and parallelism pays off.
- Re-verification after fixes dispatches a **fresh subagent** to avoid
  anchoring on the previous review.

Tests are the counter-case: test **execution** happens in the main session via
Bash (the TDD cycle needs immediate feedback), while a gate subagent reviews
**test quality** — acceptance-criteria coverage and whether tests actually
verify behavior.

## Verification Gate (references/verification-gate.md)

Used at the gates marked in the workflow (stages 1, 2, 3; report verification
in 4; wiki verification in 5).

1. Dispatch a reviewer subagent (per the invocation model above) with the
   artifact path(s) and the stage's verification criteria.
2. Classify issues **Critical / Major / Minor** by **impact on experiment
   validity and reproducibility**.
3. Fix issues immediately. Up to 3 automatic fix-and-reverify rounds.
4. Exit: only Minor remaining → pass and proceed. Major or Critical remaining
   after 3 rounds → escalate.
5. Issues that are **decisions rather than defects** escalate immediately,
   regardless of round count: stop the loop, present situation + 2–3 options.

## Stage References (purpose / deliverables / completion criteria)

Each reference states purpose, deliverables, and completion criteria, and
keeps procedure prescriptions minimal — the agent decides the how.

### 0. bootstrap (`bootstrap.md`) — run once per project
- Analyze project docs and code; restructure docs into the layout above,
  adapting to existing conventions.
- Write `glossary.md`, `index.md`; if no PRD exists, guide the user to create
  one (using `templates/prd.md`).
- **Done when:** layout exists, `index.md` reflects it, PRD present, user confirms.

### 0.5. long-term plan (`long-term-plan.md`)
- Entered from stage 1 when the user's requirement doesn't fit one loop; if
  ambiguous, ask the user.
- Produce a multi-loop plan (sequence of loop-sized goals) stored under
  `docs/wiki/` (project-persistent).
- **Done when:** user approves the plan; loop 1 scope feeds stage 1.

### 1. Experiment Design (`experiment-design.md`)
- Research recent papers/repos/blogs for context and alternatives.
- Propose 2–3 options for this loop's work, always including a free-form
  "user's own idea" option.
- Write Experiment Design Doc from template: problem definition / data /
  hypothesis / search space / model evaluation method / acceptance criteria /
  out of scope. Decision points go to the user with 2–3 alternatives.
- Verification gate → user review.
- **Done when:** user approves the doc.

### 2. Tech Design (`tech-design.md`)
- **Code grounding required.** Write Tech Design Spec: AS-IS / TO-BE based on
  the Experiment Design Doc; technical restatement of acceptance criteria;
  code review and test methods.
- Write Implementation Plan: task specs, order, dependencies, through
  verification.
- Verification gate → user review.
- **Done when:** user approves both documents.

### 3. Implementation (`implementation.md`)
- Implement tasks sequentially per the plan, TDD: write a failing test first,
  make it pass, refactor; keep tests green.
- Verification gate per the plan's checkpoints.
- **Done when:** all tasks done, tests pass, gate passed.

### 4. Experiment (`experiment.md`)
- Run data prep / training / evaluation. Long jobs run in background with
  `state.json` tracking (command, PID, log path); on resume, check job
  liveness and logs before continuing.
- Write experiment report in markdown first (template), verify, then render
  an html version for user readability.
- **Done when:** user reviews the report.

### 5. Wrap-up (`wrap-up.md`)
- Garbage collection: remove code/docs made obsolete by this loop.
- LLM wiki update:
  1. Main session computes this loop's change scope (git diff since loop
     start + human-source doc changes).
  2. One updater subagent per wiki doc-directory, dispatched in parallel,
     each given its directory and the changed-file list. Updaters check
     existing claims against code, fix stale claims, add new ones, and cite
     sources per claim. They write docs directly (disjoint directories).
  3. One verifier subagent per directory afterwards, re-checking modified
     claims against code; the main session handles only failed claims.
- Tools & hooks review: add only frequently-needed items; avoid overbuilding.
- Write `handoff_notes`, set status `done`.
- **Done when:** GC done, wiki verified, state closed.

## Templates

Six templates under `templates/`, each with section skeletons and one-line
guidance per section: PRD, Experiment Design Doc, Tech Design Spec, Task Spec,
Implementation Plan, Experiment Report.

## Out of Scope

- Install/deploy automation (copy or symlink manually; may add later).
- Project-specific experiment infra assumptions (trainers, MLflow, W&B, etc.)
  — bootstrap discovers what the target project uses.
- Multiple skills or per-stage slash commands — one skill only.

## Testing / Acceptance

- Skill passes a structural review: SKILL.md stays thin (dashboard + stage
  table + shared principles); every stage's detail lives in its reference.
- Every requirement in `prompts/build-experiment-loop.md` maps to a location
  in the skill (traceability check during review).
- All files in English; skill functions with no plugins installed.
