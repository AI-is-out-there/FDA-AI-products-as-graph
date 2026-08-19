# FDA AI-enabled medical devices as a graph

This repository explores the lineage of AI-enabled medical devices authorized by
the U.S. Food and Drug Administration (FDA). It models 510(k) submissions as a
directed graph: each node is a device submission and an edge connects a cited
predicate device to the newer submission. The result makes it possible to see
which devices are repeatedly used as predicates, how product families develop,
and how authorizations are distributed over time, medical panel, and product
code.

## Repository contents

| Path | Description |
| --- | --- |
| [`datasets/dataset-FDA-med-graph.csv`](datasets/dataset-FDA-med-graph.csv) | Source table of FDA decisions and predicate relationships. |
| [`code/Graph-analysis.ipynb`](code/Graph-analysis.ipynb) | Main NetworkX and Plotly analysis: constructs the directed graph, calculates degree statistics, and produces interactive timeline, hierarchy, and summary views. [Open in Colab](https://colab.research.google.com/github/AI-is-out-there/FDA-AI-products-as-graph/blob/main/code/Graph-analysis.ipynb). |
| [`code/Graph-FDA-timeline-intereactive.ipynb`](code/Graph-FDA-timeline-intereactive.ipynb) | Alternative NetworkX/Matplotlib and PyVis timeline visualization. [Open in Colab](https://colab.research.google.com/github/AI-is-out-there/FDA-AI-products-as-graph/blob/main/code/Graph-FDA-timeline-intereactive.ipynb). |
| [`code/fda_network_graph_main.html`](code/fda_network_graph_main.html) | Pre-generated interactive Plotly network/timeline view. |
| [`code/fda_medical_device_timeline_interactive.html`](code/fda_medical_device_timeline_interactive.html) | Pre-generated interactive PyVis network. |
| [`latex-report/`](latex-report/) | LaTeX report template, bibliography, styles, and user manual. |
| [`reports/`](reports/) | Location reserved for project reports. |

## Dataset

The semicolon-delimited CSV contains one row per device record and the following
fields:

- `Date of Final Decision` (`DD.MM.YYYY`)
- `Submission Number`
- `Device`
- `Company`
- `Panel (Lead)`
- `Primary Product Code`
- `Predicate Device`

The current snapshot has **1,302 records**, covering decisions from **15 March
2001 through 29 September 2025**. It includes 650 companies, 17 panel labels,
and 132 primary product codes. The data is dominated by Radiology submissions,
so comparisons between panels should take that class imbalance into account.

## How the analysis works

1. Parse the decision date and split predicate identifiers where a submission
   cites more than one predicate.
2. Add each submission and its metadata to a directed NetworkX graph.
3. Add an edge from each predicate to the submission that cites it. Predicate
   identifiers absent from the dataset may therefore appear as external nodes.
4. Calculate incoming, outgoing, and total degree to highlight influential
   predicates and devices that cite multiple earlier submissions.
5. Lay out devices chronologically and group them by FDA panel and product code;
   export interactive HTML views with hover details, zooming, and panel colors.

The repository is an exploratory visualization project, not an FDA regulatory
database or a clinical decision-support tool. Check records against the
authoritative FDA source before using them in research or regulatory work.

## Run the notebooks

The easiest route is to use the **Open in Colab** links above.

1. Download or clone this repository.
2. Open a notebook in Google Colab.
3. Upload `datasets/dataset-FDA-med-graph.csv` to the Colab session as
   `/content/dataset-FDA-med-graph.csv` (the path expected by both notebooks).
4. Run the cells from top to bottom. The notebooks install or import their
   Python dependencies, including pandas, NetworkX, NumPy, Matplotlib, Plotly,
   scikit-learn, ipywidgets, and PyVis.
5. Download the generated HTML output if you want to retain it after the Colab
   session ends.

Alternatively, run the notebooks locally with Jupyter after installing the same
dependencies and change the CSV path in the loading cell to
`../datasets/dataset-FDA-med-graph.csv`.

The checked-in HTML files are self-contained outputs and can be opened directly
in a modern browser; no notebook execution is required to explore them.

## Related visualization

- [Russian Healthcare AI devices diagram](https://mermaid.ai/d/6cc175a9-0f3f-44ea-8cc4-23c9ed607311) — a complementary Mermaid diagram of AI devices in Russian healthcare.

## License

This project is distributed under the terms of the [MIT License](LICENSE).
