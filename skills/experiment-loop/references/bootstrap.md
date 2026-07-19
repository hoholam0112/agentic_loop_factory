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
- `docs/CONVENTIONS.md` — the wiki's rules: document authority (human `raw/`
  is top authority), priority when sources disagree, and the frontmatter spec.
- Seeded `docs/agent/knowledge/` — initial knowledge documents filled from the
  existing code and docs (not just empty directories): data pipeline, dataset,
  model/architecture, evaluation setup, environment. Each claim cites its
  source file. Includes:
  - `code-map.md` (from `templates/code-map.md`) — where code lives and where
    to look to change something: layout, entry points, key modules, configs.
    This is where the step-1 code analysis is written down instead of thrown
    away.
  - `experiment-ledger.md` (from `templates/experiment-ledger.md`) — start it
    from any experiments already run before onboarding, else an empty table.
  - `artifact-map.md` (from `templates/artifact-map.md`) — registry of
    non-code, non-doc artifacts (checkpoints, datasets, run outputs, plots).
    Record the project's artifact storage convention (where they live) and any
    artifacts that already exist; else an empty table.
- `docs/agent/guidance/human-feedback.md` (from `templates/human-feedback.md`)
  — log of human corrections and stated preferences, read before every stage's
  work. Seed it with any standing preferences the user gives during onboarding;
  else empty.
- `CLAUDE.md` at the project root (English) — harness guidance for Claude Code:
  build/test/run commands, project conventions, and the communication rule
  (explain to the user in plain Korean; see SKILL.md "Plain language").
- A PRD in `docs/human/raw/`. If none exists, guide the user through writing one
  using `templates/prd.md`: interview them section by section — the user
  authors the content, you scribe.
- Required MCP servers identified and the user guided to install them (see
  procedure step 5). Bootstrap does not install them itself.

## Procedure outline (adapt as needed)

1. Analyze the project: README, existing docs, code structure,
   experiment/training entry points, test setup. Read the code, not just the
   docs.
2. Propose the layout adaptation to the user (what moves where, what gets
   created). Get agreement before moving any files.
3. Restructure. Classify existing docs on three axes: management subject
   (human-authored `human/raw/` vs agent-generated `agent/`), usage period
   (loop-scoped `agent/loops/` vs project-persistent `agent/knowledge/`, with
   decision records in `agent/knowledge/decisions/` and agent working-rules in
   `agent/guidance/`), and topic. Human-view outputs go in `shared/`. Group
   persistent project docs by topic into `docs/agent/knowledge/` subdirectories
   so later stages can update them in parallel. Write `glossary.md` and
   `CONVENTIONS.md`.
4. Seed the wiki: from the code and existing docs, write the initial
   `docs/agent/knowledge/` documents (code map, data pipeline, dataset, model,
   eval setup, environment). Write the step-1 code analysis into `code-map.md`.
   Decide where experiment artifacts are stored and record the convention plus
   any existing artifacts in `artifact-map.md`. Create
   `docs/agent/guidance/human-feedback.md` (seeded with any standing
   preferences, else empty). Ground every claim in a source file. Then write
   `CLAUDE.md` at the project root with build/test/run commands, conventions,
   and the plain-Korean communication rule.
5. Identify required MCP servers for this project's work (e.g. web search for
   paper research, a Git host, a data source). List them for the user with
   why each is needed, and guide them to install — do not install yourself.
   If none are needed, say so.
6. Ensure the PRD exists. Then write `index.md` last — its presence is how
   the dashboard detects a completed onboarding, so it must not exist before
   everything else is in place.

**Done when:** layout exists, wiki seeded, `CLAUDE.md` written, MCP servers
handled, `index.md` reflects it, PRD present, and the user confirms. Then
start the first loop (stage 1).
