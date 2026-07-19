# NLP-Driven Support Ticket Intelligence System

An end-to-end analytics pipeline that turns raw support tickets into business intelligence — sentiment scoring, zero-shot category classification, and AI-generated complaint summaries, surfaced through a 3-page Power BI dashboard. Processes 70,000+ tickets end to end, no manual reprocessing after refresh.

`Python` · `pandas` · `VADER` · `HuggingFace Inference API` · `SQLite` · `Power BI`

---

## Architecture

```
Raw ticket CSV (70K+ records)
        │
        ▼
Clean & engineer features  →  ticket_length, urgency_score, is_high_urgency
        │
        ▼
VADER sentiment scoring     →  compound score + Positive/Neutral/Negative
        │
        ▼
Zero-shot classification    →  bart-large-mnli predicts category, no training data
        │
        ▼
Load into SQLite            →  5 business queries (window functions, CTEs)
        │
        ▼
AI summarization            →  bart-large-cnn summarizes top negative tickets/category
        │
        ▼
Power BI                    →  3-page dashboard, reads from exported CSVs
```

---

## Core Components

| Component | Role |
|---|---|
| pandas | Cleaning, feature engineering |
| VADER | Rule-based sentiment scoring — fast, explainable, no training |
| HuggingFace `bart-large-mnli` | Zero-shot category classification |
| HuggingFace `bart-large-cnn` | AI-generated complaint summaries |
| SQLite | Local SQL layer — no server required |
| Power BI | Executive overview, NLP deep dive, AI insights |

---

## Dataset

Customer Support Tickets (Bitext, via Kaggle) — 70,000+ records. Fully free: no paid APIs, no cloud infrastructure.

---

## Pipeline Stages

| Stage | Description |
|---|---|
| **Raw** | Ticket text and metadata as downloaded |
| **Cleaned** | Normalized text, parsed dates, engineered features |
| **Enriched** | VADER sentiment + zero-shot predicted category |
| **Insights** | SQL aggregations + AI summaries, exported for BI |

---

## Key Insights

- Billing tickets drive **38% of all negative sentiment** despite only 22% of volume
- Billing shows **~2.4x higher negative sentiment** than technical tickets — a pricing/transparency issue, not a reliability one
- Mismatch analysis surfaces **hidden dissatisfaction**: 4-5 star ratings paired with negative-sentiment text
- Findings are framed as recommendations (e.g. a dedicated billing resolution workflow), not just observations

---

## Dashboard

1. **Executive Overview** — KPI cards, monthly volume trend, sentiment split, volume by category
2. **NLP Deep Dive** — sentiment by category, urgency heatmap, top keywords, ticket length vs. sentiment
3. **AI Insights** — HuggingFace-generated complaint summary per category, paired with its sentiment score

---

## Analytical Queries

1. Which categories have the lowest average sentiment, and what volume do they represent?
2. How do ticket volume and sentiment trend month over month?
3. How does each category's sentiment compare to the overall benchmark? *(window function)*
4. What percentage of each category's tickets are high-urgency?
5. Which categories fall below a negative-sentiment threshold? *(CTE)*

---

## Design Decisions

**VADER over a transformer** — purpose-built for short, informal text; fast and explainable at this scale, where a deep model would be overkill.

**Zero-shot over keyword rules** — `bart-large-mnli` assigns categories it was never explicitly trained on, using natural language understanding rather than hand-built keyword dictionaries.

**Rate-limit aware calls** — HuggingFace's free tier throttles requests, so classification and summarization calls are spaced out and validated on a sample before running on the full dataset.

---

## How to Run

```bash
pip install -r requirements.txt
```

Run notebooks in order: `01_eda` → `02_cleaning_features` → `03_sentiment_nlp` → `04_classification` → `05_sql_analysis` → `06_ai_summaries`. Then open the Power BI file and refresh against `/outputs`.

```
├── data/raw/
├── data/processed/
├── notebooks/
├── scripts/
├── dashboard/
├── outputs/
└── screenshots/
```
