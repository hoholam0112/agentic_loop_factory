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

## Layout

- `docs/agent/knowledge/` — persistent learnings, grouped by topic.
- `docs/agent/decisions/` — decision records (ADR): why a choice was made.
- `docs/agent/loops/<loop-id>/` — loop-scoped, ephemeral (GC target).
- `docs/shared/` — human-view outputs (reports, summaries).

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
