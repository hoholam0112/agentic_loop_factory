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
- A PRD in `docs/human/`. If none exists, guide the user through writing one
  using `templates/prd.md`: interview them section by section — the user
  authors the content, you scribe.

## Procedure outline (adapt as needed)

1. Analyze the project: README, existing docs, code structure,
   experiment/training entry points, test setup. Read the code, not just the
   docs.
2. Propose the layout adaptation to the user (what moves where, what gets
   created). Get agreement before moving any files.
3. Restructure. Classify existing docs on three axes: management subject
   (human-authored vs agent-generated), usage period (loop-scoped vs
   project-persistent), and topic. Group persistent wiki docs by topic into
   `docs/wiki/` subdirectories so later stages can update them in parallel.
   Write `glossary.md` and `index.md`.
4. Ensure the PRD exists.

**Done when:** layout exists, `index.md` reflects it, PRD present, and the
user confirms. Then start the first loop (stage 1).
