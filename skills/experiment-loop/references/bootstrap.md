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
- A PRD in `docs/human/raw/`. If none exists, guide the user through writing one
  using `templates/prd.md`: interview them section by section — the user
  authors the content, you scribe.

## Procedure outline (adapt as needed)

1. Analyze the project: README, existing docs, code structure,
   experiment/training entry points, test setup. Read the code, not just the
   docs.
2. Propose the layout adaptation to the user (what moves where, what gets
   created). Get agreement before moving any files.
3. Restructure. Classify existing docs on three axes: management subject
   (human-authored `human/raw/` vs agent-generated `agent/`), usage period
   (loop-scoped `agent/loops/` vs project-persistent `agent/knowledge/`, with
   decision records in `agent/decisions/`), and topic. Human-view outputs go
   in `shared/`. Group persistent wiki docs by topic into
   `docs/agent/knowledge/` subdirectories so later stages can update them in
   parallel. Write `glossary.md` and `CONVENTIONS.md`.
4. Ensure the PRD exists. Then write `index.md` last — its presence is how
   the dashboard detects a completed onboarding, so it must not exist before
   everything else is in place.

**Done when:** layout exists, `index.md` reflects it, PRD present, and the
user confirms. Then start the first loop (stage 1).
