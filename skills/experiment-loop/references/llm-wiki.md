# LLM Wiki

The agent-generated knowledge base under `docs/agent/`. It keeps project
context current and easy for agents to search and reuse across loops.

## Writing principles

- Keep project context continuously up to date so agents can search and reuse
  it.
- Claim-level sourcing: every claim in an agent-authored document cites its
  source of truth at the file level (and line where useful).
- Update the wiki at the end of each stage, not only at wrap-up.
- `docs/CONVENTIONS.md` governs the wiki: document authority (human `raw/` is
  top authority), priority when sources disagree, and the frontmatter spec.

## Layout — what goes where

- `docs/agent/knowledge/` — persistent facts that stay true across loops,
  grouped by topic into subdirectories. Contents: how the data pipeline works,
  dataset descriptions, model/architecture notes, the evaluation setup,
  recurring error patterns, environment/infra notes, the experiment ledger
  (`experiment-ledger.md` — every experiment run so far, one row each), and the
  long-term plan (`long-term-plan.md`) if one exists. NOT single-loop results
  or decisions. One file per topic; update it in place as the project changes.
- `docs/agent/decisions/` — decision records (ADR), one file per significant
  choice: what was decided, why, alternatives considered, consequences.
  Examples: "why LoRA rank 16", "why dataset X was dropped". Append-only —
  record a new ADR that supersedes an old one rather than rewriting history.
- `docs/agent/loops/<loop-id>/` — everything scoped to a single loop,
  ephemeral (GC target): `experiment-design.md`, external-research findings,
  `tech-design-spec.md`, task specs, `implementation-plan.md`,
  `experiment-report.md`, logs, and `state.json`. Consumed within the loop;
  at wrap-up its lasting parts are promoted to `knowledge/` or `decisions/`
  and the rest is collected.
- `docs/shared/` — human-view outputs: the HTML experiment report, summaries.

**Where does a new document go?** Persistent and reusable → `knowledge/`
(new topic → new file/subdirectory; existing topic → append to that file).
A choice and its rationale → `decisions/` (new ADR file). Only meaningful
within this loop → `loops/<loop-id>/`. For humans to read → `shared/`.
Whenever you add, move, or delete a document, update `docs/index.md`.

## Update procedure (used at wrap-up)

1. Compute the change scope:
   `git diff --name-only <state.json.start_commit>..HEAD` plus changes to
   human-authored docs.
2. Dispatch one updater subagent per `docs/agent/knowledge/` subdirectory in
   parallel (Agent tool, `general-purpose` type) with the updater prompt
   below. Updaters write directly — directories are disjoint, so no conflicts.
3. Dispatch one verifier subagent per updated directory with the verifier
   prompt below. The main session fixes only failed claims, then re-verifies
   the fixed claims once with a fresh verifier. If a claim still fails, delete
   it and note it in `handoff_notes` — do not loop further.
4. Garden the wiki:
   - Place promoted content (see wrap-up) using the "where does a new document
     go?" rule above — new topic → new file/subdirectory under `knowledge/`,
     existing topic → append.
   - Restructure when a `knowledge/` subdirectory has grown too large to scan
     or its topic has blurred: split or merge subdirectories so each stays a
     single clear topic. Only restructure when the need is real — do not churn
     structure every loop.
   - Update `docs/index.md` to reflect every added, moved, or deleted document,
     and `docs/glossary.md` if terminology changed.

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
