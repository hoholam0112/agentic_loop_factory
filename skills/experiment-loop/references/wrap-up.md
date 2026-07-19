# Stage 5: Wrap-Up

**Purpose:** leave the project clean and the knowledge base current; close
the loop.

## 1. Garbage collection

Identify code, configs, and docs made obsolete by this loop (superseded
experiments, dead flags, scratch scripts). Confirm with the user before
deleting anything this loop did not create. Commit deletions separately.

## 2. LLM wiki update

Run the wiki update procedure in `references/llm-wiki.md` (change-scope diff →
parallel updater subagents per `docs/agent/knowledge/` subdirectory → verifier
subagents → garden the wiki). Gardening covers where new documents go,
restructuring `knowledge/` when a subdirectory outgrows its topic, and updating
`index.md`/`glossary.md` for every added, moved, or deleted document. If this
loop changed the code structure (moved files, new entry points/modules),
refresh `docs/agent/knowledge/code-map.md`.

## 3. Promote loop-scoped content

Review `docs/agent/loops/<loop-id>/` for anything worth keeping beyond this
loop. Promote persistent learnings into `docs/agent/knowledge/` and decision
records into `docs/agent/decisions/`; leave the rest for garbage collection.

Append this loop's experiments to the experiment ledger
(`docs/agent/knowledge/experiment-ledger.md`): one short row per experiment run
this loop (a loop with several variants gets several rows) — loop id,
hypothesis, key setup, key result, outcome — citing this loop's report. Keep
cells short; detail stays in the report. This keeps the ledger the single
up-to-date index stage 1 checks to avoid repeats.

## 4. Tools & hooks review

Review this loop's friction: commands typed repeatedly, mistakes a hook
could have caught. Add a tool or hook ONLY if the need occurred at least
twice this loop. Avoid overbuilding — none is a fine outcome.

## 5. Close the loop

- Write `state.json.handoff_notes`: what the next loop should know —
  unfinished threads, surprises, recommended next goal.
- Update `docs/agent/knowledge/long-term-plan.md` if it exists.
- Set `status: "done"`.

**Done when:** all five sections are complete.
