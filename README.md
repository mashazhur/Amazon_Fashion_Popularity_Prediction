# Predicting Product Popularity in Amazon Fashion: The Role of Online Review Signals

Code and analysis for my MSc Business Analytics & Management master thesis at Rotterdam 
School of Management, Erasmus University Rotterdam (2026).

## Overview

This study investigates to what extent publicly available online review signals predict 
short-term product popularity on Amazon Fashion. Using a strictly temporal predictive 
framework, review signals measured at month t−1 are used to predict product popularity
operationalised as review volume at month t. The analysis examines five review signal 
dimensions (review volume, star ratings, sentiment, helpful votes, and verified purchase 
share), engineers a set of interaction features, and tests whether predictive power differs 
between strong and weak brands.

## Research Questions

**Main RQ:** To what extent do different review characteristics predict short-term product 
popularity on Amazon Fashion?

- **SRQ1:** How do review sentiment, star ratings, and review volume affect future popularity?
- **SRQ2:** Do credibility- and usefulness-related cues (helpful votes, verified purchase 
  share) improve predictability beyond sentiment and ratings?
- **HRQ1 & HRQ2:** Do these predictive relationships differ between strong and weak brands?

## Data

- **Source:** Amazon Reviews 2023 dataset (McAuley Lab, UC San Diego) — 
  [https://amazon-reviews-2023.github.io/](https://amazon-reviews-2023.github.io/index.html)
- **Category:** Amazon Fashion
- **Files used:** `Amazon_Fashion.jsonl` (user reviews) and `meta_Amazon_Fashion.jsonl` (item metadata)
- **Timeframe:** 2014–2023
- **Final dataset:** 26,606 product-month observations across 4,351 unique products
- **Unit of analysis:** product-month (`parent_asin` × `year_month`)

> **Note:** The raw `.jsonl` data files are not included in this repository due to size. 
> Download them from the McAuley Lab link above and place them in the working directory 
> before running the notebook.

## Pipeline

The analysis runs end-to-end in `analysis_code_650614.ipynb`:

1. **Load & filter reviews** — stream `Amazon_Fashion.jsonl` in chunks, convert timestamps, 
   filter to 2014–2023
2. **Clean** — drop duplicates, inspect missing/empty values, drop unused columns
3. **Aggregate to product-month** — compute `n_reviews`, `avg_rating`, `avg_helpful`, 
   `verified_share`, `avg_review_length`
4. **Filter** — keep product-months with ≥5 reviews and products with ≥2 consecutive months
5. **Sentiment extraction** — VADER compound score on title + body (`full_text`), aggregated to product-month
6. **Feature engineering** — lagged volume, cumulative prior reviews, sentiment volatility, 
   sentiment-rating gap, sentiment change, verified sentiment signal, helpful sentiment 
   impact, unverified hype
7. **Merge metadata** — attach `store` (brand) via left join on `parent_asin`
8. **Multicollinearity check** — VIF analysis to define regression vs. tree-based feature sets
9. **Temporal split** — train (2014–2019), validation (2020–2021), test (2022–2023)
10. **Modelling** — naïve baseline, Ridge/Lasso/Elastic Net, Random Forest, XGBoost (+ early stopping)
11. **Heterogeneity analysis** — brand strength split (median brand review total), models 
    re-run per strong/weak subgroup
12. **Descriptive statistics & visualisations**
