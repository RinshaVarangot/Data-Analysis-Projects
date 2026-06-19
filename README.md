# E-Commerce Data Pipeline

A end-to-end data science pipeline for e-commerce analysis and refund prediction, built in Python. Covers data ingestion and merging across six relational tables, exploratory data analysis, feature engineering, and machine learning.

**Prediction target:** Whether an order will be refunded (`has_refund` — binary classification)

## Overview

| Metric | Value |
|--------|-------|
| Total orders | 32,313 |
| Total revenue | $1,938,509.75 |
| Overall refund rate | 5.33% |
| Features engineered | 27 |
| Best model (AUC) | Random Forest — 0.6492 |

---

## Dataset

Six CSV files from an e-commerce platform, covering the full customer journey from website session to purchase and potential refund.

| File | Rows | Description |
|------|------|-------------|
| `orders.csv` | 32,313 | Core order records with price, COGS, and session link |
| `order_items.csv` | 40,025 | Individual line items within each order |
| `order_item_refunds.csv` | 1,731 | Refund transactions tied to order items |
| `products.csv` | 4 | Product catalogue with names and launch dates |
| `website_sessions.csv` | 472,871 | Website sessions with UTM parameters and device info |
| `website_pageviews.csv` | 1,188,124 | Page-level navigation events per session |

---

## Project Structure

```
ecommerce_pipeline.ipynb        # Main notebook — all pipeline stages
ecommerce_pipeline_report.docx  # Full written analysis report

outputs/
├── merged_ecommerce.csv        # Unified dataset (32,313 rows × 29 columns)
├── eda_overview.png            # Order volume, revenue, device split, UTM sources, refund rate by product
├── eda_correlations.png        # Correlation heatmap, gross margin, AOV by device, hourly order pattern
├── business_insights.png       # Revenue by channel, day-of-week trends, new vs. repeat AOV
├── feature_correlations.png    # Pearson correlation of each feature with the refund target
├── ml_evaluation.png           # ROC curves, precision-recall curves, AUC comparison, confusion matrices
└── feature_importance.png      # Random Forest feature importance ranking
```

---

## Pipeline

### 1. Data Merging

All six files are joined via a sequential left-join chain anchored on `orders`, producing one row per order with all associated session, product, and refund data.

```
order_items
    ├── + order_item_refunds   (join on order_item_id → refund flag per item)
    └── + products             (join on product_id → product name)
    └── aggregate to order level (item counts, revenue, COGS, refund totals)

orders
    ├── + order aggregates     (join on order_id)
    ├── + website_sessions     (join on website_session_id → UTM, device, repeat flag)
    └── + pageview aggregates  (join on website_session_id → funnel depth, page counts)
```

Final shape: **32,313 rows × 29 columns**. Saved as `outputs/merged_ecommerce.csv`.

### 2. Exploratory Data Analysis

Key findings from the 2012–2015 dataset:

- **Revenue growth** — Order volume and revenue grew consistently over the 3-year period.
- **Device split** — Desktop accounts for the majority of orders; mobile converts at a lower average order value.
- **Traffic sources** — `gsearch` (Google Search) is the dominant acquisition channel, followed by `bsearch` and organic/direct.
- **Price clustering** — Orders concentrate at $49.99 and $59.99, with $109.98 for multi-item orders.
- **Repeat sessions** — 19% of converting sessions come from returning visitors.
- **Peak hours** — Order volume is highest mid-morning to early afternoon, consistent with a US customer base.
- **Refund rate** — 5.33% overall (1,723 refunded orders out of 32,313).

Missing values were limited to UTM columns (~19%) for direct-traffic sessions, which carry no UTM tags by nature.

### 3. Feature Engineering

27 features created across five groups. See [Feature Engineering](#feature-engineering) for the full list.

> ⚠️ `refund_rate` and `refund_intensity` are derived directly from the target variable. They were created for analytical purposes only and **excluded from the ML feature set** to prevent data leakage.

### 4. Machine Learning

Binary classification task with a stratified 80/20 train-test split and 5-fold cross-validation. Class imbalance (5.33% positive rate) was addressed using `class_weight='balanced'` across all models.

Three models were trained and evaluated — see [Model Results](#model-results).

---

## Feature Engineering

| Group | Features |
|-------|----------|
| **Time** | `order_year`, `order_month`, `order_dayofweek`, `order_hour`, `is_weekend`, `order_quarter` |
| **Revenue & Margin** | `gross_margin`, `margin_pct`, `cogs_ratio`, `revenue_per_item` |
| **Web Engagement** | `pages_per_visit`, `funnel_depth`, `reached_cart`, `reached_billing` |
| **Marketing** | `utm_source_enc`, `utm_campaign_enc`, `device_type_enc` |
| **Order** | `items_purchased`, `price_usd`, `cogs_usd`, `is_repeat_session`, `primary_product_id` |

---

## Model Results

| Model | AUC | Avg Precision | F1 (Refund) | CV-AUC |
|-------|-----|---------------|-------------|--------|
| **Random Forest** ⬅ best | **0.6492** | 0.0854 | 0.15 | 0.6099 ± 0.008 |
| Gradient Boosting | 0.6477 | 0.0873 | 0.01 | 0.5986 ± 0.011 |
| Logistic Regression | 0.5855 | 0.0784 | 0.12 | 0.5792 ± 0.012 |

Random Forest achieved the highest AUC (0.6492) and most stable cross-validation score. Top predictive features by importance: `price_usd`, `primary_product_id`, and order timing variables.

---

## Requirements

- Python 3.8+
- pandas
- numpy
- matplotlib
- seaborn
- scikit-learn

Install all dependencies:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

---

## Usage

1. Place the six CSV files in a local directory.
2. Open `ecommerce_pipeline.ipynb` and update the `BASE` path variable at the top of the notebook to point to that directory.
3. Run all cells in order — each section builds on the previous one.
4. All outputs (merged dataset and charts) are saved to the path defined by the `OUT` variable.

```python
BASE = "/path/to/your/data"   # folder containing the 6 CSV files
OUT  = "/path/to/outputs"     # where merged_ecommerce.csv and charts are saved
```
