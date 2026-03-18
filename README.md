AI Patent Landscape Analysis\
Tools: Python · Power BI · Pandas · Matplotlib\
Data: USPTO Artificial Intelligence Patent Dataset (AIPD) · 15.4M records · 1976–2023

Overview\
This project analyses over 15 million patent records from the USPTO to map the growth and composition of AI innovation globally across 8 technology subfields — Machine Learning, Computer Vision, Natural Language Processing, Speech Recognition, Planning & Reasoning, Evolutionary Computation, Knowledge Representation, and AI Hardware.\
The analysis was built to handle a dataset too large for standard in-memory processing, using a chunked Python pipeline to clean, engineer features, and aggregate the data before visualising it in a 3-page interactive Power BI dashboard.

Key Findings

15,437,542 total patents in the dataset\
2,182,808 classified as AI patents at 86% confidence threshold — 14.14% of all patents\
Peak filing year: 2023 with 169,136 AI patents\
Top subfield: Planning & Reasoning (23.58% of all AI patents)\
CAGR 2010–2022: 7.3% per year\
Growth multiple since 2010: 2.3x

Python Pipeline\
The raw dataset (~15.4M rows) exceeds typical in-memory capacity, so the pipeline runs in two passes using Pandas chunked reading (chunksize=200,000):\
Pass 1 — Clean & Feature Engineer

Parses publication dates, extracts year and month\
Filters to valid range (1976–2024)\
Converts all flag/score columns to numeric, fills nulls with 0\
Computes dominant AI subfield per patent using idxmax() on AI score columns\
Creates binary classification flags at 50%, 86%, and 93% confidence thresholds\
Writes processed output to a new CSV to avoid re-reading the raw file

Pass 2 — Aggregate

Reads the processed CSV in 100K-row chunks\
Accumulates yearly totals, subfield-by-year counts, dominant subfield counts, and decade summaries into defaultdict accumulators\
Builds 7 aggregated tables at completion — no full dataset ever held in memory\
Derives Table 3 (subfield summary) directly from the subfield-by-year accumulator to avoid redundant bookkeeping


Power BI Dashboard\
The dashboard connects to the 7 aggregated CSVs via a star schema data model with 01_yearly_totals as the central fact table.\
Page 1 — The Big Picture

4 KPI cards: total patents, AI patents, AI share %, peak year\
AI patent filings over time (1990–2023) with Deep Learning Era annotation\
AI patent volume by decade\
Year-over-year growth rate (2005–2023)

Page 2 — Subfield Deep Dive

Tile slicer for selecting technology area\
Stacked area chart showing subfield composition over time (responds to slicer)\
Donut chart showing permanent share distribution across all subfields\
KPI cards for market share %, recent filings, and total patents (respond to slicer)

Page 3 — The Modern Surge

Drill-through target from Page 1 (right-click any year → drill through)\
Labeled bar chart: AI patent filings 2015–2023\
Subfield trajectory lines over the surge period\
Confidence tier comparison (50% vs 86% vs 93% thresholds)


Data Source\
USPTO Artificial Intelligence Patent Dataset (AIPD)\
Published by the United States Patent and Trademark Office\
Available at: https://www.uspto.gov/ip-policy/economic-research/research-datasets/artificial-intelligence-patent-dataset\
The dataset classifies patents across 8 AI subfields using machine learning models at three confidence thresholds (50%, 86%, 93%). This analysis uses the 86% threshold as the primary classification standard.

How to Run

Download the AIPD dataset from the USPTO link above\
Update CSV_PATH in aipd_analysis.ipynb to point to your local file\
If running in Google Colab, mount your Drive and update the path accordingly\
Run all cells — Pass 1 writes a processed CSV, Pass 2 generates the powerbi_tables/ directory\
Open Power BI Desktop and import the 7 CSVs from powerbi_tables/\
Set up relationships as described in the data model section above

Requirements\
pandas\
numpy\
matplotlib\
seaborn
