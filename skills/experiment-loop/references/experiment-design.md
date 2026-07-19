# Stage 1: Experiment Design

**Purpose:** decide what this loop experiments on, and specify it well enough
that tech design can start.

## Deliverables

- New loop directory `docs/agent/loops/<loop-id>/` (`loop-id` = next
  zero-padded sequence + slug) with `state.json` initialized: stage 1,
  `in_progress`, `start_commit` set to the current commit hash (used by
  wrap-up's change-scope diff).
- `experiment-design.md` in the loop directory, from
  `templates/experiment-design-doc.md`: problem definition / data /
  external research / prior error review / hypothesis / search space /
  evaluation method / constraints / acceptance criteria / out of scope.

## Procedure outline

1. Load context progressively (glossary → index → needed docs). Read the
   relevant code to ground the current state.
2. Research recent papers and open-source repositories relevant to the
   problem (web search). Capture findings with sources; write the full
   findings to a separate file and keep only a summary + references in the
   design doc's external-research section.
3. Review prior errors: read the previous loop's experiment report (its Error
   Analysis section) and relevant knowledge/decision notes. Summarize the main
   error patterns and decide which this loop will address. This feeds the
   hypothesis and search space. If this is the first loop, note that there is
   no prior report.
4. Check the experiment ledger (`docs/agent/knowledge/experiment-ledger.md`) —
   the list of every experiment already run. Confirm the proposed direction is
   not a repeat. If it overlaps a completed experiment, state what is different
   and why re-running is worth it (e.g. fixing a flaw in the earlier run). Read
   the referenced report(s) when an entry looks close.
5. Propose 2-3 candidate directions for this loop with trade-offs and a
   recommendation. Always include a free-form option for the user's own
   idea. If the chosen direction doesn't fit one loop, enter stage 0.5 —
   at most once per loop; if the scope still doesn't fit after 0.5,
   escalate instead of re-entering.
6. Write the design doc. For every section, present the user a question with
   2-3 options (include a free-form option for their own input). Keep the
   writing plain — detailed but easy, readable by an undergraduate.
7. Verification gate (references/verification-gate.md). Criteria:
   - the hypothesis is falsifiable;
   - the evaluation method can actually measure the acceptance criteria;
   - the data section matches the real data (checked against code/files);
   - the scope is achievable in one loop;
   - the prior error review reflects the previous loop's actual report (or
     notes that this is the first loop);
   - the experiment is not a duplicate of a ledger entry, or the design says
     what differs and why re-running is justified.
8. Request user review (`status: awaiting_user_review`); revise until
   approved.

**Done when:** user approves the doc. Set state to stage 2.
