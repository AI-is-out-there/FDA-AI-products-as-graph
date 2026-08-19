# FDA AI-enabled medical devices as a graph

An exploratory graph of AI-enabled medical devices authorized by the U.S. Food
and Drug Administration (FDA). Each node represents a 510(k) submission and a
directed edge runs from a predicate device to the later submission that cites
it. This representation helps reveal recurring predicates, evolving product
families, and authorization patterns across time, medical panels, and product
codes.

> [!IMPORTANT]
> This repository is a visualization project, not an authoritative FDA
> regulatory database or a clinical decision-support tool. Verify records with
> the FDA before using them for research, clinical, or regulatory decisions.

## Explore the results

No Python setup is required to view the checked-in visualizations. Download or
clone the repository, then open either HTML file in a modern browser:

- [`code/fda_network_graph_main.html`](code/fda_network_graph_main.html) —
  Plotly timeline and hierarchical network view with zoom and hover details.
- [`code/fda_medical_device_timeline_interactive.html`](code/fda_medical_device_timeline_interactive.html)
  — PyVis network view with draggable nodes, zoom, and hover details.

Because the HTML files contain their visualization data and JavaScript, they
are large (approximately 5.3 MB and 450 KB respectively) but can be viewed
offline.

## Repository map

| Path | Purpose |
| --- | --- |
| [`datasets/dataset-FDA-med-graph.csv`](datasets/dataset-FDA-med-graph.csv) | Semicolon-delimited source snapshot containing FDA decisions and predicate relationships. |
| [`code/Graph-analysis.ipynb`](code/Graph-analysis.ipynb) | Primary NetworkX/Plotly workflow: cleans the data, builds the graph, calculates network statistics, creates a hierarchical timeline, and exports Plotly HTML. [Open in Colab](https://colab.research.google.com/github/AI-is-out-there/FDA-AI-products-as-graph/blob/main/code/Graph-analysis.ipynb). |
| [`code/Graph-FDA-timeline-intereactive.ipynb`](code/Graph-FDA-timeline-intereactive.ipynb) | Alternative NetworkX/Matplotlib/PyVis timeline workflow. (The misspelling in the filename is retained for compatibility.) [Open in Colab](https://colab.research.google.com/github/AI-is-out-there/FDA-AI-products-as-graph/blob/main/code/Graph-FDA-timeline-intereactive.ipynb). |
| [`code/fda_network_graph_main.html`](code/fda_network_graph_main.html) | Pre-generated Plotly output. |
| [`code/fda_medical_device_timeline_interactive.html`](code/fda_medical_device_timeline_interactive.html) | Pre-generated PyVis output. |
| [`latex-report/`](latex-report/) | Springer Nature LaTeX article template and supporting style files; it is not a completed project report. |
| [`reports/`](reports/) | Placeholder for future reports. |

## Dataset snapshot

The CSV has **1,302 rows** and covers final decisions from **15 March 2001 to
29 September 2025**. It contains **650 companies**, **17 panel labels**, and
**132 primary product codes**. Radiology accounts for 1,022 records (about
78.5%), so panel comparisons should account for this substantial class
imbalance.

The file uses a semicolon (`;`) delimiter and `DD.MM.YYYY` dates:

| Column | Meaning |
| --- | --- |
| `Date of Final Decision` | FDA final-decision date in `DD.MM.YYYY` format. |
| `Submission Number` | 510(k) submission identifier used as the graph node ID. |
| `Device` | Device trade or model name. |
| `Company` | Applicant/company name. |
| `Panel (Lead)` | Lead FDA medical-specialty panel label. |
| `Primary Product Code` | Primary FDA product code. |
| `Predicate Device` | One or more predicate submission IDs; multiple IDs are comma-separated in some rows. |

Minimal Python loading example:

```python
import pandas as pd

devices = pd.read_csv(
    "datasets/dataset-FDA-med-graph.csv",
    sep=";",
    parse_dates=["Date of Final Decision"],
    date_format="%d.%m.%Y",
)
```

## Graph methodology

The notebooks use the following general workflow:

1. Parse the decision dates and attach each row's metadata to a node keyed by
   `Submission Number`.
2. Read predicate identifiers from `Predicate Device`.
3. Add a directed edge `predicate -> submission` when the predicate identifier
   is also present in this dataset.
4. Arrange nodes chronologically and color/group them by lead panel or product
   code.
5. Use node degree to summarize connections and export an interactive HTML
   visualization.

With the current snapshot, splitting comma-separated predicate values produces
**805 unique internal edges**. Predicate references that do not have their own
row in the snapshot are skipped; the notebooks do **not** create external
predicate nodes. Consequently, the graph describes relationships visible
within this dataset rather than a complete FDA predicate lineage.

For an edge `predicate -> submission`, the predicate's **out-degree** counts
later submissions in this snapshot that cite it, while a submission's
**in-degree** counts its included predicates. This direction is useful to keep
in mind when interpreting degree labels in notebook output.

## Run in Google Colab

1. Open either notebook using its **Open in Colab** link above.
2. Upload [`dataset-FDA-med-graph.csv`](datasets/dataset-FDA-med-graph.csv) to
   the Colab session as `/content/dataset-FDA-med-graph.csv`, which is the path
   currently hard-coded in both notebooks.
3. Run all cells from top to bottom.
4. Download the generated HTML before the temporary Colab session ends.

The primary notebook writes `/content/fda_network_graph_main.html`. The
alternative notebook writes `fda_medical_device_timeline.html` and
`fda_medical_device_timeline_interactive.html` in its current working
directory.

## Run locally

Python 3.10 or newer is recommended. Create an isolated environment and install
the notebook dependencies:

```bash
python -m venv .venv
source .venv/bin/activate        # Windows PowerShell: .venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install jupyter pandas networkx numpy matplotlib plotly scikit-learn ipywidgets pyvis
jupyter lab
```

Before running a notebook locally, change its CSV loading path from
`/content/dataset-FDA-med-graph.csv` to
`../datasets/dataset-FDA-med-graph.csv`. You may also want to change output
paths under `/content/` to paths inside `code/`.

## Current limitations

- The data is a dated repository snapshot and does not update automatically.
- Only predicates also represented by a dataset row become graph edges;
  external or older predicates are omitted.
- Company, panel, and device labels are used as supplied and may contain naming
  variants (for example, two Gastroenterology/Urology spellings occur).
- The primary notebook treats `Predicate Device` as a single identifier, while
  the alternative notebook splits comma- or semicolon-separated identifiers.
  Therefore, regenerated graphs can differ when a row contains multiple
  predicates.
- The notebooks are Colab-oriented and include package-installation cells or
  hard-coded `/content/` paths, so local execution requires the adjustments
  described above.
- Checked-in HTML files are generated artifacts and may not reflect later CSV
  edits unless the notebooks are rerun and the outputs replaced.

## Related visualization

- [Russian Healthcare AI devices diagram](https://mermaid.ai/d/6cc175a9-0f3f-44ea-8cc4-23c9ed607311)
  — a complementary Mermaid diagram of AI devices in Russian healthcare.

## License

Distributed under the terms of the [MIT License](LICENSE).
