# Experiment Report (HTML)

How to turn the markdown experiment report into a polished HTML report for the
user. Output goes to `docs/shared/`.

## Writing principles

- Detailed but easy. Avoid hard words; use jargon only when necessary. An
  undergraduate should be able to read and understand it without effort.
- Design like a senior frontend engineer: use HTML, CSS, and JavaScript to
  make a clean, refined report.
- The final result is a single self-contained HTML file. It must open directly
  in a browser with no install step (inline all CSS/JS; embed assets).
- Make five separate tabs: Overview, Data, Model, Experiment History, Error
  Analysis.

## Tabs and their contents

- **Overview** — background, problem definition, data, experiment history,
  accuracy, performance metrics.
- **Data** — collection / preprocessing / composition / sample data.
- **Model** — a detailed explanation of each model tried, with pseudo code and
  formulas. Include prompts verbatim (original text).
- **Experiment History** — which experiments were run so far and how they
  turned out, explained with a table.
- **Error Analysis** — at least 20-30 sampled cases (both wrong and correct),
  laid out so the input data, ground-truth answer, and model output are all
  visible at a glance.

## Source of truth

Ground every number and claim in the markdown report and its cited output
files. The HTML is a presentation of the markdown report, not a new source.
