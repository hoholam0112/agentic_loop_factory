# Experiment Report (HTML)

How to turn the markdown experiment report into a polished, self-contained HTML
report for the user. Output goes to `docs/shared/<loop-id>-report.html`.

**Start from the scaffold — don't build from scratch.** Copy
`templates/experiment-report.html`, then replace every `FILL` marker with real
content. The scaffold already carries the design system, the 5-tab layout, and
working inline-SVG chart helpers, so quality doesn't depend on improvising CSS
or a chart library. Do not restyle it from zero; fill it in.

## Writing principles

- Detailed but easy. Avoid hard words; use jargon only when necessary, and gloss
  it the first time. An undergraduate should read it without effort.
- Self-contained in content: the reader understands the whole experiment from
  the report alone, without opening any code, config, or other document. Restate
  the needed context (data, method, metric meanings) inline. Cited files are
  evidence, not pointers the reader must follow to understand.
- Self-contained as a file: one `.html` that opens directly in a browser with no
  install step. Inline all CSS and JS; embed any image as a data URI. No external
  stylesheets, fonts, scripts, or CDN links — they may be blocked and the file
  must work offline.
- Ground every number and claim in the markdown report and its cited output
  files. The HTML presents the markdown report; it is not a new source.

## Tabs and their contents

Five tabs, matching the scaffold: Overview, Data, Model, Experiment History,
Error Analysis.

- **Overview** — background, problem definition, headline metrics (as KPI
  tiles), one summarizing chart, and a result summary that states each
  acceptance criterion pass/fail.
- **Data** — collection, preprocessing, composition (split sizes in a table),
  and 3-5 real sample rows shown verbatim.
- **Model** — each model/method tried, explained plainly, with pseudocode,
  formulas (every symbol defined), and every prompt verbatim.
- **Experiment History** — one row per run in a table plus a trend chart, and a
  prose narrative of what changed between runs and why (a story, not just
  numbers).
- **Error Analysis** — 20-30 real cases (mix of wrong and correct), each showing
  input, ground-truth answer, and model output at a glance, plus discussion of
  the error patterns.

## Design system (already in the scaffold)

The scaffold's CSS holds these; keep them, don't override:

- **Color** via CSS custom properties (`--accent`, `--ok`, `--bad`, categorical
  `--c1..--c6`); light and dark both handled by `prefers-color-scheme`. Use the
  categorical colors for chart series, never hand-picked hex.
- **Type**: one system sans stack for text, one mono stack for code/prompts. Set
  sizes from the `--fs-*` scale; don't introduce new sizes.
- **Layout**: centered column (`--maxw`), cards and KPI tiles on `--surface`
  with `--border` and soft shadow, consistent `--gap` spacing and `--radius`.
- **Components** ready to duplicate: KPI tile, card, table (wrap wide tables in
  `.table-scroll`), correct/wrong badge, and the error-case block.

## Charts (inline SVG, no library)

The scaffold defines two helpers; use them and pass real numbers. Never add a
charting library — a CDN link will be blocked and break the file.

- `barChart(elId, {data:[{label,value}], unit})` — compare a metric across runs
  or categories.
- `lineChart(elId, {series:[{name, points:[[x,y],...]}], xlabel})` — a trend over
  epochs/steps; multiple series get a legend automatically.

Pick the form by data: **bar** for comparisons across a few items, **line** for
a trend over an ordered axis, **table** when exact values matter more than shape.
If a chart doesn't help, use a table instead of forcing one.

## Extend for the reader

The scaffold's tabs and default charts are the floor. Once they are filled with
real content, stop and ask: *what would make THIS result click for the user?*
Then add it — a chart the defaults don't cover (a per-slice breakdown, a
confusion-style view, a before/after comparison), an extra table, a short
callout of the one thing that surprised you. Add charts by copying the
`figure.chart` block and calling `barChart`/`lineChart` with real data; the
scaffold supports as many as you add. Judge each addition by "does this help
the reader understand?", not by decoration — and never invent data, additions
draw from the same output files.

## Verification gate

Run the gate (references/verification-gate.md) on the HTML before handing it to
the user. **Open the rendered file in a browser and look** — do not judge from
the source. Criteria:

- **Opens and renders**: loads with no console errors; every tab switches and
  shows content; every chart draws with real data (no empty SVG, no leftover
  demo numbers).
- **No placeholders**: no `FILL` marker remains anywhere (grep the file); every
  tab holds real content, not scaffold hints.
- **Depth matches the markdown report**: Data has real sample rows and split
  sizes; Model has pseudocode, defined formulas, and verbatim prompts; History
  has a filled table plus narrative; Error Analysis has 20-30 real cases. Flag
  any thin or generic section as Major.
- **Numbers trace**: every figure and chart value matches the markdown report
  and its cited output files.
- **Self-contained**: no external CSS/JS/font/image references; file works with
  no network.
- **Readable**: plain language, jargon glossed, understandable without opening
  code or other docs.
