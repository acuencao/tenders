# Data

`part-00000-b5be9405-11c0-4c04-a771-bac38e228ad8-c000.snappy.parquet` is the dataset used by every notebook in this repository.

## Source

Contract award notices from the *TED CSV Open Data v3.3* files published by the European Commission (Tenders Electronic Daily, Supplement to the Official Journal of the EU). The files were processed with an Apache Spark ETL pipeline described in Section 3 of the article.

## Content

- **159,752** contract award notices, years **2015–2023**, **30** countries (`ISO_COUNTRY_CODE`).
- **25** columns: `DURATION`, `YEAR`, `ID_TYPE`, `LOTS_NUMBER`, `VALUE_EURO`, `CRIT_PRICE_WEIGHT`, `NUMBER_AWARDS`, `NUMBER_OFFERS`, `NUMBER_TENDERS_SME`, `B_MULTIPLE_CAE`, `CAE_NAME`, `CAE_TOWN`, `CAE_GPA_ANNEX`, `ISO_COUNTRY_CODE`, `CAE_TYPE`, `MAIN_ACTIVITY`, `B_ON_BEHALF`, `TYPE_OF_CONTRACT`, `CPV`, `TOP_TYPE`, `CRIT_CRITERIA`, `CRIT_WEIGHTS`, `WIN_COUNTRY_CODE`, `B_CONTRACTOR_SME`, `B_SUBCONTRACTED`.
- Numerical variables are stored on their original scale (not normalized).

## Filters applied

Only active, non-canceled procedures awarded to the most economically advantageous tender, with contract values between 1,000 and 50 million euros, were kept (Table 3 of the article).

## Derived variables

These are built inside the notebooks, not stored in the file:

- `SME_WIN` (target of Experiment B): 1 if at least one lot was awarded to an SME, from `B_CONTRACTOR_SME` (multi-lot values such as `y---n---y`).
- `GROUP_CPV`: first two digits of `CPV`.
- One-hot dummies of the categorical columns; the selected predictors for each experiment are listed in the notebooks and in Table 6 of the article.

## Loading

```python
import pandas as pd
df = pd.read_parquet("data/part-00000-b5be9405-11c0-4c04-a771-bac38e228ad8-c000.snappy.parquet")
```

Reading parquet requires `pyarrow` (included in `requirements.txt`).
