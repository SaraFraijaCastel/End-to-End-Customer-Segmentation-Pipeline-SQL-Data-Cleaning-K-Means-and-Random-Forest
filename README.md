# Customer Segmentation for Advertising Campaigns

End-to-end data science project: from a **dirty SQL database** to a **customer segmentation model** that assigns new customers to marketing segments.

The pipeline covers data generation, SQL exploration, data cleaning, unsupervised clustering (K-Means) and supervised classification (Random Forest).

## Project structure

```
publicity/
├── data/
│   ├── raw/
│   │   ├── advertising.db               # SQLite database with dirty customer data
│   │   └── raw_customers.csv            # Raw extraction from the database
│   └── processed/
│       ├── clean_customers.csv          # Cleaned dataset
│       └── segmented_customers.parquet  # Clean data + K-Means segment labels
├── models/
│   └── segmentation_model.pkl           # Trained Random Forest classifier
├── notebooks/
│   ├── 01_create_database.ipynb
│   ├── 02_sql_extraction.ipynb
│   ├── 03_data_cleaning.ipynb
│   ├── 04_kmeans_segmentation.ipynb
│   ├── 05_random_forest_classifier.ipynb
│   └── 06_interpretation.ipynb
├── requirements.txt
└── README.md
```

## Pipeline

| # | Notebook | Description | Output |
|---|---|---|---|
| 01 | `01_create_database.ipynb` | Generates 550 synthetic customer records with intentional data quality issues (missing values, junk text, inconsistent categories, mixed formats, outliers, duplicates) and stores them in SQLite. | `data/raw/advertising.db` |
| 02 | `02_sql_extraction.ipynb` | Explores the data quality issues with plain SQL and extracts the table into pandas. | `data/raw/raw_customers.csv` |
| 03 | `03_data_cleaning.ipynb` | Cleans the data step by step: junk → `NaN`, duplicate removal, category normalization, regex parsing of amounts, IQR outlier handling, date standardization and median/mode imputation. | `data/processed/clean_customers.csv` |
| 04 | `04_kmeans_segmentation.ipynb` | Scales the features, picks K with the elbow method and clusters customers into 4 segments. | `data/processed/segmented_customers.parquet` |
| 05 | `05_random_forest_classifier.ipynb` | Trains a Random Forest to predict the segment, analyzes feature importance and compares a baseline vs. a refined model. | `models/segmentation_model.pkl` |
| 06 | `06_interpretation.ipynb` | Predicts the segment of new customers and sanity-checks the predictions. | — |

## Customer segments

| Segment | Profile |
|---|---|
| **Ghosts** | Fewest clicks and conversions — they barely engage |
| **Whales** | Highest spend; convert a lot from few impressions |
| **Browsers** | Most clicks, but low conversions and the lowest spend |
| **Core Buyers** | Most impressions and the highest conversions, solid spend |

## Key results

- **Data cleaning:** 550 dirty rows → 499 clean rows with no missing values and consistent categories.
- **Classification:** the refined Random Forest (4 behavioural features: conversions, impressions, clicks, monthly spend) reaches **91 % accuracy**, versus 87 % for the baseline model using all features.
- Ghosts are detected with a **recall of 1.00**, so no marketing budget is wasted on customers who won't engage.

## Getting started

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook notebooks/
```

Run the notebooks in order (`01` → `06`). Each notebook reads the output of the previous one, using paths relative to the `notebooks/` folder.
