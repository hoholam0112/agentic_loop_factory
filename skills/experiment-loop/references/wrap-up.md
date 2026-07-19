# Stage 5: Wrap-Up

**Purpose:** leave the project clean and the knowledge base current; close
the loop.

## 1. Garbage collection

Identify code, configs, and docs made obsolete by this loop (superseded
experiments, dead flags, scratch scripts). Confirm with the user before
deleting anything this loop did not create. Commit deletions separately.

Keep this loop's record documents in `docs/agent/loops/<loop-id>/` (design
doc, spec, plan, report, state.json) — the ledger and maps cite them and later
loops read them. GC inside a loop directory targets only scratch and
intermediate working files, never these records.

Then collect artifacts using the artifact map
(`docs/agent/knowledge/artifact-map.md`) — not the git diff, since artifacts
are often untracked. For each entry: keep what is needed to reproduce or reuse
(final checkpoints, eval outputs the report cites, expensive-to-rebuild
datasets); delete `temp` ones (intermediate epochs, scratch). Confirm before
deleting anything large or shared across loops. Update the map for every
artifact deleted or kept.

## 2. LLM wiki update

Run the wiki update procedure in `references/llm-wiki.md` (change-scope diff →
parallel updater subagents per `docs/agent/knowledge/` subdirectory → verifier
subagents → garden the wiki). Gardening covers where new documents go,
restructuring `knowledge/` when a subdirectory outgrows its topic, and updating
`index.md`/`glossary.md` for every added, moved, or deleted document. If this
loop changed the code structure (moved files, new entry points/modules),
refresh `docs/agent/knowledge/code-map.md`.

## 3. Promote loop-scoped content

Consolidate the loop log (`docs/agent/loops/<loop-id>/loop-log.md`) into the
persistent stores. Sort each important item by the "Which bucket?" rule in
`references/llm-wiki.md`:
- A rule about HOW the agent should work (a correction or preference to carry
  forward) → `docs/agent/guidance/human-feedback.md`.
- A choice about WHAT to build or run, and why → an ADR in
  `docs/agent/knowledge/decisions/` (`templates/decision-record.md`).
- A current fact about the project (new/changed behavior, config, data) →
  update the relevant `docs/agent/knowledge/` doc in place.

The loop's record documents (including the log) stay in place (see section 1);
only scratch is collected.

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
