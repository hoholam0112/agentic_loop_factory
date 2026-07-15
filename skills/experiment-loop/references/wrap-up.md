# Stage 5: Wrap-Up

**Purpose:** leave the project clean and the knowledge base current; close
the loop.

## 1. Garbage collection

Identify code, configs, and docs made obsolete by this loop (superseded
experiments, dead flags, scratch scripts). Confirm with the user before
deleting anything this loop did not create. Commit deletions separately.

## 2. LLM wiki update

1. Compute the change scope:
   `git diff --name-only <state.json.start_commit>..HEAD` plus changes to
   human-authored docs.
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
