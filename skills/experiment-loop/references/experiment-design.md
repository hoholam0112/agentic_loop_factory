# Stage 1: Experiment Design

**Purpose:** decide what this loop experiments on, and specify it well enough
that tech design can start.

## Deliverables

- New loop directory `docs/loops/<loop-id>/` (`loop-id` = next zero-padded
  sequence + slug) with `state.json` initialized: stage 1, `in_progress`,
  `start_commit` set to the current commit hash (used by wrap-up's
  change-scope diff).
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
   idea. If the chosen direction doesn't fit one loop, enter stage 0.5 —
   at most once per loop; if the scope still doesn't fit after 0.5,
   escalate instead of re-entering.
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
