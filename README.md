# Do Breakfast Grocery Prices Affect McDonald's Menu Prices? (2026)

A Python data analysis of McDonald's US menu prices, global breakfast-staple grocery prices, and US family income, testing whether grocery costs move menu prices and whether eating out became relatively more attractive in 2026.

> **Short answer:** In this data, grocery prices show **no detectable effect** on menu prices, and nutrition **correlates with** price but does **not predict** it. Menu items did get cheaper relative to income, but the data contains no sales or traffic, so it cannot show whether people's behavior changed. See [Limitations](#limitations).

## Datasets

| File | What it contains |
|---|---|
| `data/mcdonalds_menu_clean.csv` | 80 menu items: category, 2026 price, nutrition facts, and a `price_history` field (2023–2026) |
| `data/breakfast_basket_clean.csv` | Monthly grocery prices (Oct 2025 – Mar 2026) for 14 items in 122 cities, plus FAO and USDA inflation indicators |
| `data/US income 2026 - Sheet1.csv` | US Census Table F-1: upper income limit of each family-income quintile, 1947–2025 (current and 2025 dollars) |

**Note:** `Breakfast_Basket_USD` is exactly the sum of four items: milk (1 L) + bread (500 g) + eggs (12) + rice (1 kg). It is a staple index, not a full breakfast.

## Methods

1. **Descriptive statistics:** mean, median and mode for menu and basket prices, plus category breakdowns.
2. **Time trends:** menu price history (2023–2026) against basket prices (Oct 2025 – Mar 2026).
3. **Correlation:** price against nutrition; FAO food index against basket price.
4. **Machine learning:** linear regression and random forest predicting menu price, scored with 5-fold cross-validation.
5. **Feature selection:** `SelectKBest` (F-test), `RFE` with a random forest, and `LassoCV`.
6. **Income analysis:** cleaned the messy Census sheet, computed income growth, menu and basket cost as a share of weekly income, and a backtested forecast of 2026 income limits.

## Key results

**Prices**

| | Mean | Median | Mode |
|---|---|---|---|
| McDonald's item price | $4.08 | $3.98 | $1.67 |
| Breakfast basket (all cities) | $9.56 | $9.16 | $10.65 |

- Burgers ($5.88) and Salads ($5.76) are the priciest menu categories; Beverages ($2.38) are the cheapest.
- Average menu price fell every year: $4.38 (2023), $4.29 (2024), $4.21 (2025), $4.08 (2026). All 80 items cost less in 2026 than in 2023.
- The staple basket rose 3.7% globally and 3.1% in the US between Oct 2025 and Mar 2026 (US: $15.02 to $15.49 on the monthly mean).

**Groceries vs menu prices**

- Groceries moved up while menu prices moved down, so there is no sign of pass-through.
- FAO food index vs basket price: correlation −0.45 over only 6 months.

**What predicts menu price?**

- Strongest correlations: sodium (0.67), protein (0.59), trans fat (0.57), total fat (0.54).
- Models fail out of sample: cross-validated R² was −0.91 (linear) and −0.08 (random forest).
- Feature selection: sodium is the only feature chosen by all three methods. Selection does not improve accuracy (R² stays near 0).
- Sodium likely stands in for item type (burgers vs drinks) rather than driving price.

**Income**

- Family income limits grew 3.9%–4.5% a year (nominal) from 2019 to 2025, but only 0.2%–0.8% a year after inflation. The lowest quintile grew slowest in real terms.
- An average menu item took 0.49% of the lowest quintile's weekly income in 2023 and 0.44% in 2025.
- The 4-staple basket took about 1.6% of the lowest quintile's weekly income versus 0.38% for the fourth quintile.
- The US basket costs about as much as 3.0 average McDonald's breakfast items ($15.49 vs $5.14 each). New York is the priciest city ($16.91), Houston the cheapest ($14.20).
- Forecast: a trailing 5-year growth method had the lowest backtest error (1.6% MAPE on 2021–2025) and projects the lowest-quintile limit at about $53,000 in 2026.

## Figures

![Prices and trends](outputs/analysis.png)
![Income analysis](outputs/analysis_income.png)

## Limitations

- **No shared key:** The menu has yearly prices; the basket is monthly for 6 months. Item-level or month-level regression between them is impossible.
- **Short grocery history:** Six months cannot show lagged effects, and price changes typically reach menus with a delay.
- **Tiny overlaps:** Income, menu and basket overlap for only 1–3 years, so correlations there (for example −0.99 between menu price and income) are not statistically meaningful.
- **No demand data:** There are no visits, sales or purchase records. The analysis shows relative costs, not behavior, and says nothing about cultural factors.
- **Data realism:** Menu prices falling every year for every item is unusual for real fast food, so conclusions may not transfer to real-world pricing.
- **Income measure:** Quintile upper limits are cutoffs for families of any size, not average incomes.

## How to run

```bash
git clone https://github.com/<your-username>/mcdonalds-grocery-analysis.git
cd mcdonalds-grocery-analysis
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python analysis.py
```

Run it from the repository root. It prints all results and saves the figures to `outputs/`.

## Repository structure

```
.
├── analysis.py          # full analysis
├── requirements.txt
├── data/                # three input CSVs
└── outputs/             # figures and results.txt (saved console output)
```

## Data sources

Menu and basket files are provided as supplied (cleaned versions). Income data: U.S. Census Bureau, Current Population Survey, Table F-1. Basket data cites Numbeo, FAO and USDA in its own columns.
