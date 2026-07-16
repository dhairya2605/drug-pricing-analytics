# Indian Drug Pricing & Market Access Analytics

SQL analytics project examining pricing power, market concentration, and brand
premium across ~254,000 real Indian pharmaceutical products — built to answer
the kind of question a pharma commercial analytics team 
or a pricing strategy team actually asks: *where does branding let a company
charge more than a competitor selling the exact same drug?*

## Data source
[junioralive/Indian-Medicine-Dataset](https://github.com/junioralive/Indian-Medicine-Dataset)
— 253,973 medicines with price (INR), manufacturer, pack size, and active
ingredient composition, sourced from real Indian pharmacy listings.

## What was built
- **Normalized schema** (`sql/01_schema.sql`): 3 tables — `manufacturers`
  (7,647), `compositions` (1,582 active ingredients), `medicines` (253,969
  products) — replacing one flat CSV with a proper relational design.
- **7 performance indexes** (`sql/02_indexes.sql`)
- **20 business SQL queries** (`sql/03_business_queries.sql`) covering price
  segmentation, pricing-power/brand-premium detection, market concentration
  (including an HHI-style index), manufacturer portfolio analysis, and
  cost-per-dose by drug form. Uses CTEs, window functions, and subqueries.
- **4 views** (`sql/04_views.sql`) built specifically to plug into Power BI
  without any further transformation.
- **1 stored procedure + 1 trigger** (`sql/05_procedures_triggers.sql`) — the
  trigger logs every price change to an audit table, modeling how a real
  pricing pipeline would track price history over time.

## Key findings
- For the median competitively-sold drug (5+ manufacturers), the most
  expensive branded version costs **~19x** the cheapest version of the exact
  same molecule — pure brand premium, since composition is identical.
- Combination drugs (2+ active ingredients) average **₹127** vs **₹384** for
  single-ingredient drugs — counterintuitively, single-ingredient originator
  drugs (many of them oncology/specialty) carry far higher average prices.
- Injectable drugs average the highest cost-per-unit (~₹657/unit) of any
  form, consistent with lower price sensitivity in hospital-administered
  categories.
- The most price-fragmented markets (e.g. Ceftriaxone: 1,698 manufacturers)
  show the classic signature of a highly genericized, price-competitive
  molecule — a useful signal for where a pharma company would need volume,
  not price, strategy to compete.

## Setup
```bash
psql -U postgres -c "CREATE DATABASE drug_pricing;"
psql -U postgres -d drug_pricing -f sql/01_schema.sql
psql -U postgres -d drug_pricing -c "\COPY manufacturers FROM 'data/manufacturers.csv' WITH (FORMAT csv, HEADER true)"
psql -U postgres -d drug_pricing -c "\COPY compositions FROM 'data/compositions.csv' WITH (FORMAT csv, HEADER true)"
psql -U postgres -d drug_pricing -c "\COPY medicines FROM 'data/medicines.csv' WITH (FORMAT csv, HEADER true)"
psql -U postgres -d drug_pricing -f sql/02_indexes.sql
psql -U postgres -d drug_pricing -f sql/04_views.sql
psql -U postgres -d drug_pricing -f sql/05_procedures_triggers.sql
```

## Next steps (Power BI)
Connect Power BI to the `drug_pricing` Postgres database and build visuals
directly off `vw_composition_market_summary`, `vw_manufacturer_portfolio`,
and `vw_price_by_form` — no further transformation needed.


## Future Enhancements
- Interactive Power BI dashboard (coming soon).
