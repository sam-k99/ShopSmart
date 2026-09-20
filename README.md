# ShopSmart Analytics

## What This Project Is About

This project analyzes over 1 million transactions from an online retail dataset to answer three business questions:
1. Did our Summer Glow marketing campaign actually work?
2. Who are our most valuable customers, and who is about to leave?
3. What products do our best customers buy together?

The goal was not just to run code, but to deliver insights that a non-technical stakeholder (like Sarah, our marketing lead) could act on immediately.

## Key Findings

### 1. Summer Glow Campaign Worked, But Not How We Expected
-   **Result:** +6.7% revenue lift ($46,614/month extra)
-   **Surprise:** The top 10 products only accounted for 10.9% of campaign revenue. This means the campaign succeeded because of broad customer engagement, not because of a few hero products.
-   **Actionable Advice:** Do not concentrate next year's budget on specific SKUs. Maintain the broad assortment strategy.

### 2. We Have a Retention Problem
-   **Finding:** 44% of registered customers have only ever bought once. Our "champions" are not perfect 5-5-5 scorers; they are 5-4-5 (high recency/spend, medium frequency), reflecting our wholesale-heavy customer base.
-   **Actionable Advice:** Launch a targeted campaign to convert one-time buyers into repeat purchasers. Investigate why frequency caps at score 4 for high-spend customers.

### 3. Customers Love Buying Color Variants Together
-   **Top Bundle:** Red and White Heart T-Light Holders (604 co-purchases among champions)
-   **Pattern:** Lunch bags, jumbo bags, and home decor frames are frequently bought in matching colors or themes.
-   **Actionable Advice:** Create "Complete the Set" bundles at checkout. Test thematic collection pricing instead of individual item discounts.

## What Went Wrong

### Skewed Data Broke Standard RFM Scoring
`pd.qcut(q=5)` assumes data can be evenly split into quintiles. In e-commerce, 44% of customers buy only once, creating duplicate bin edges that crash the function.
**Fix:** Implemented dynamic label generation using `retbins=True` and `duplicates='drop'`. This accepts fewer than 5 tiers when the data warrants it, producing accurate segments instead of forcing artificial splits.

### Empty Placeholder Notebooks Caused Load Errors
Using `touch` to create notebook placeholders produces empty text files, not valid JSON notebooks. Jupyter Lab throws `NotJSONError` when trying to open them.
**Fix:** Only create notebook files through Jupyter Lab's interface or by copying existing valid notebooks. Never pre-create empty placeholders.

### Triple-Quoted F-Strings Are Indentation-Sensitive
Multi-line f-strings with triple quotes require exact indentation matching for opening and closing delimiters. Any deviation causes `_IncompleteInputError`.
**Fix:** Use parenthesized string concatenation for multi-line formatted text blocks. This eliminates indentation sensitivity while preserving readability and variable interpolation.

## Project Structure

```
ShopSmart/
├── notebooks/
│   ├── 01_data_cleaning_and_exploration.ipynb   # Loading, type fixing, filtering, validation
│   ├── 02_campaign_evaluation.ipynb             # Summer Glow lift analysis vs historical baseline
│   ├── 03_rfm_segmentation.ipynb                # Customer segmentation with dynamic scoring
│   ├── 04_product_affinity.ipynb                # Market basket analysis among champion customers
│   └── 05_visualization_dashboard.ipynb         # Stakeholder-ready charts and summary card
├── src/                                          # Reusable Python modules (future extraction)
├── tests/                                        # Unit tests for src/ modules (future)
├── Data/                                         # Raw dataset (excluded from Git tracking)
├── requirements.txt                              # Exact Python dependencies for reproducibility
└── README.md                                     # This file
```

Notebooks are numbered to enforce execution order. Each builds on the previous one and can be run independently after Cell 1 (data loading).

## How to Reproduce This Analysis

1.  Clone this repository: `git clone https://github.com/sam-k99/ShopSmart.git`
2.  Create a virtual environment: `python -m venv venv && source venv/bin/activate`
3.  Install dependencies: `pip install -r requirements.txt`
4.  Download the Online Retail II dataset from Kaggle and place `online_retail_II.csv` in the `Data/` folder
5.  Open Jupyter Lab and run notebooks sequentially: `01_` → `02_` → `03_` → `04_` → `05_`

All charts are saved as high-DPI PNGs in the `notebooks/` directory and can be used directly in presentations without re-running code.

## Technical Details Worth Remembering

-   **RFM Scoring:** Uses dynamic quintile binning with `duplicates='drop'` to handle skewed e-commerce distributions. Labels are sliced to match actual bin count, not forced to 5.
-   **Campaign Evaluation:** Compares same-month YoY performance (Summer 2011 vs Summer 2010), not overall averages. This controls for seasonality and avoids false positives.
-   **Product Affinity:** Analyzes co-purchases only among champion-equivalent customers (RFM 54x+), not all customers. This reveals high-value bundling opportunities, not general popularity.
-   **Git Safety:** Uses local sparse checkout to permanently exclude `Data/` folder from tracking. This operates below the ignore layer and cannot be bypassed by `git add -f` or auto-scripts.
