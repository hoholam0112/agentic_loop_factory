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
2. Find the latest loop state: `docs/agent/loops/*/state.json` (directories sort
   chronologically by name). If none exists, offer: (a) start the first loop
   (stage 1), (b) run bootstrap (stage 0).
3. If state exists, summarize `loop_id`, `stage`, `status`, `current_task`,
   plus any `pending_decisions` and running `jobs`. Offer:
   (a) continue from the recorded point, (b) start a new loop,
   (c) re-enter a specific stage, (d) run bootstrap (stage 0). If the latest
   loop's `status` is `done`, the natural default is (b).
4. Always include the bootstrap option in the choices above, even when the
   project is already onboarded — it re-runs stage 0 to restructure docs,
   re-seed the wiki, refresh `CLAUDE.md`, or revisit MCP setup. It normally
   runs once per project, so confirm intent before restructuring existing docs.
5. Load the reference for the chosen stage and proceed.

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
- **Claim-level sourcing.** Every claim in an agent-authored document cites
  its source file (and line where useful). Applies to all stages — design
  docs, specs, plans, reports, and wiki alike.
- **Progressive context loading.** Don't load everything up front. On user
  input: read `docs/glossary.md` and align terminology (ask when a user's
  term is ambiguous), then read `docs/index.md` and open only the documents
  the task needs.
- **Escalation.** Stop the loop, set `status: "escalated"`, record the
  question in `state.json.pending_decisions`, describe the situation to the
  user, and present 2-3 options.
- **Verification gates** run in subagents per
  `references/verification-gate.md`. After a stage's gate passes, summarize
  the stage's work to the user before moving on.
- **Language.** All docs and code in English. `docs/glossary.md` may contain
  Korean. Communicate with the user in Korean (technical terms in English).
- **Plain language.** When explaining to the user (before and after work,
  and at gate summaries), write so it reads without effort. Concretely:
  - State the conclusion first, then a short reason.
  - One idea per sentence; keep sentences short.
  - Use a technical term only when necessary, and add a short plain gloss the
    first time (e.g. "검증 게이트(결과물을 다시 검사하는 단계)").
  - Avoid transliterated English loanwords when a plain Korean word exists
    (write "출처를 따라갈 수 있는지", not "추적성"; "효과 큰 순서", not "레버리지 순").
  - Prefer concrete verbs over abstract nouns ("검사한다", not "검증을 수행한다").

## Project Layout

Default layout. Bootstrap adapts it to existing project conventions and
records the result in `docs/index.md`; later stages follow `index.md`, not
this default.

```
docs/
  index.md              # navigation for agents (agent-maintained)
  glossary.md           # human<->agent terminology
  CONVENTIONS.md        # wiki rules: authority, priority, frontmatter spec
  human/
    raw/                # human-authored originals (top authority; agents never edit)
  agent/                # agents read and write; humans don't read this
    knowledge/          # persistent learnings, grouped by topic
    decisions/          # decision records (ADR): why a choice was made
    loops/<loop-id>/    # loop-scoped, ephemeral (GC target): design doc, spec, plan, report, state.json
  shared/               # agents write, humans read: reports, summaries (e.g. HTML report)
```

The **LLM wiki** (`docs/agent/knowledge/`, with ADRs in `docs/agent/decisions/`)
exists to keep project context current and easy for agents to search and reuse
across loops. Its writing principles and update procedure live in
`references/llm-wiki.md`; it is maintained at each loop's wrap-up (see
`references/wrap-up.md`).

`loop-id` = zero-padded sequence + slug, e.g. `003-lora-rank-sweep`.

## state.json

`docs/agent/loops/<loop-id>/state.json` — the minimum for resume-after-interruption
and handoff to the next loop. Update on every stage transition, escalation,
and job start.

```json
{
  "loop_id": "003-lora-rank-sweep",
  "stage": 3,
  "status": "in_progress",
  "start_commit": "a1b2c3d",
  "current_task": "eval harness (task 4/6)",
  "artifacts": {"experiment_design": "docs/agent/loops/003-lora-rank-sweep/experiment-design.md"},
  "jobs": [{"command": "python train.py ...", "pid": 12345, "log": "docs/agent/loops/003-lora-rank-sweep/logs/train.log", "outputs": "runs/003/"}],
  "pending_decisions": [],
  "handoff_notes": null
}
```

`status`: `in_progress` | `awaiting_user_review` | `escalated` | `done`.
Extend fields as needed; keep these as the minimum.

## Templates

Use `templates/` when creating documents: `prd.md`,
`experiment-design-doc.md`, `tech-design-spec.md`, `task-spec.md`,
`implementation-plan.md`, `experiment-report.md`, `experiment-ledger.md`,
`code-map.md`, `artifact-map.md`.
