# Resume Screening Automation

A Python pipeline to screen large volumes of resume PDFs and produce a ranked candidate scorecard, built under a real time constraint.

## Context

With **388 applicants** and **4 days** to deliver an interview shortlist for a data science / ML engineering role, manual review wasn't feasible. I was brought in to assist and proposed building an automated screening pipeline from scratch. This notebook is that pipeline.

## What it does

1. **Bulk PDF ingestion** — walks a directory, extracts text from all PDFs using `pdfplumber`, one row per page
2. **Text normalization** — strips encoding artifacts, collapses whitespace
3. **Manual overrides** — optional CSV to supply corrected text for resumes where PDF extraction fails
4. **Feature extraction** (regex-based):
   - Highest degree attained (PhD, Master's in AI/DS/ML, Master's other, Bachelor's, none)
   - GCP professional certifications
   - Years of experience (context-aware, with negative-context filtering)
   - 20+ skill and JD-specific signals (SQL, Python, ML/AI, GCP, Vertex AI, agentic AI, MLOps, deployment, drift monitoring, and more)
5. **Weighted composite scoring** — each signal contributes a tuned point value calibrated to the job description; a mention cap prevents verbosity from inflating scores
6. **CSV export** — sorted, ready for stakeholder review
7. **Visualization** — score distribution and signal breakdown by tier

## Design choices

- **Rule-based extraction over statistical NLP** — deliberate choice for speed, explainability, and auditability in a hiring context
- **JD-specific signal set** — built iteratively after reviewing what applicants actually write, not just generic skill keywords
- **Mention cap** — `MENTION_CAP = 10` rewards signal presence, not repetition
- **Warning flags** — `LOW_TEXT_EXTRACT`, `NULL_BYTES`, `MANUAL_TEXT_OVERRIDE` surface extraction issues without silently dropping candidates

## Results

Score distribution across 388 resumes:

| Tier | Score range | Count |
|------|-------------|-------|
| Lower | 0–12 | 180 |
| Mid | 13–24 | 190 |
| Top | 25–36 | 18 |

## Setup

```bash
pip install pdfplumber pandas matplotlib
```

Update the **Configuration** cell at the top of the notebook with your local paths before running:

```python
RESUME_FOLDER = "~/data/resumes"        # folder containing PDF resumes
RESULTS_DIR   = "~/data/results"        # output directory
```

The pipeline auto-creates `RESULTS_DIR` if it doesn't exist.

## Privacy note

Resume PDFs and output CSVs are not included in this repository. The notebook contains no candidate data.

## Tech stack

Python · pdfplumber · pandas · regex · matplotlib
