# Stage 2: Tech Design

**Purpose:** design the code changes needed to run the experiment, grounded
in the actual codebase.

**Code grounding is mandatory here:** read every module the design touches
before writing AS-IS. The AS-IS section must cite real files.

## Deliverables (both in the loop directory)

- `tech-design-spec.md` from `templates/tech-design-spec.md`:
  - AS-IS / TO-BE, based on the Experiment Design Doc;
  - acceptance criteria restated technically — the exact command, metric,
    and threshold that decides each one;
  - verification plan: how the code will be reviewed and tested.
- `implementation-plan.md` from `templates/implementation-plan.md`:
  - tasks (each specified per `templates/task-spec.md`), their order and
    dependencies, and verification checkpoints — covering implementation
    through verification.

## Procedure outline

1. Write the spec, then the plan.
2. Verification gate (references/verification-gate.md). Criteria:
   - AS-IS matches the real code;
   - TO-BE is sufficient to run the designed experiment;
   - every acceptance criterion has a technical verification;
   - tasks are ordered, dependency-correct, and individually testable.
3. Request user review; revise until approved.

**Done when:** user approves both documents. Set state to stage 3.
