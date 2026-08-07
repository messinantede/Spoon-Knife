# Report Spec — ERP_Data: Delivery, Invoicing & Discount Analysis

## Report identity

- **Report**: ERP_Data (existing PBIP at `ERP Data/ERP_Data.Report`, live connection `byConnection`)
- **Semantic model**: ERP_Data — workspace **Retail CPG V2** (dataset `e9f9fb3a-32ea-4a4c-a45f-725fb0cc2bff`)
- **Mode**: Brownfield — **add 3 new pages** to the existing report; existing pages (Page 1, Product Order Analysis, Customer Analysis, Orders and Invoices) are kept unchanged
- **Canvas**: 1280 × 720 (matches existing pages)

## User decisions (locked)

| Question | Decision |
|---|---|
| Audience | Executives / leadership |
| Report shape | 3 new pages: Delivery & Waste, Invoicing & Gap, Discount Evolution |
| "Region" dimension | **Customer city** (`Customers[City]`) |
| Target | Add pages to existing ERP_Data PBIP |
| Design feel | Operational Monitor — status colors, exception highlighting, clean grid |

## Requirements (verbatim mapping)

1. **Delivery delay analysis** — "How many orders delivered, by products, by region, for top customers" → Page 1
2. **Waste analysis** — "number of orders never delivered" → Page 1 (KPI + detail table)
3. **Orders invoiced analysis** — "How many? % of orders not invoiced by region, by customers, top 3 customers analysis, gap between order amount and invoiced amount" → Page 2
4. **Discount analysis** — "discount amounts on a monthly basis per country, evolution throughout the years" → Page 3

## Verified data facts (live model inspection)

- 830 orders, 2,155 order lines, 91 customers, 77 products, 21 countries; OrderDate range **1996-07-04 → 1998-05-06**
- **Delivered (shipped) = 809**, **never delivered = 21** (blank `ShippedDate`), **late deliveries = 37** (`ShippedDate > RequiredDate`), avg days to ship = 8.5
- **Every order is invoiced** (830/830). "% of orders not invoiced" is 0% — shown as a compliance KPI, and the analysis is reframed around the **amount gap**
- **Order amount (net) $1,265,793.04 vs invoiced amount $1,265,793.06** — total gap is rounding-level (~$0.02). The gap visual surfaces per-customer line-level rounding differences and will act as an exception detector if future data diverges
- `ShipRegion` populated only 323/830 → per user decision, geography = `Customers[City]`
- Total discount = $88,665.55 (6.5% of gross $1,354,458.59)
- No dedicated Date table — trends bind to `Orders[OrderDate]` (month/year hierarchy)

## Existing measures (reused)

`Order Count`, `Shipped Orders`, `Unshipped Orders`, `Avg Days To Ship`, `Sales Amount`, `Gross Sales`, `Discount Amount`, `Customer Count`, `Sales Per Customer`, `Orders Per Customer`, `Order Lines`, `Units Sold`, `Average Order Value`, `Average Units Per Order`

## Model requirements (new measures — via semantic-model-authoring)

| Measure | Home table | Definition (DAX intent) | Format |
|---|---|---|---|
| Delivered % | Orders | `DIVIDE([Shipped Orders], [Order Count])` | 0.0% |
| Never Delivered % | Orders | `DIVIDE([Unshipped Orders], [Order Count])` | 0.0% |
| Late Deliveries | Orders | `COUNTROWS(FILTER(Orders, NOT ISBLANK(Orders[ShippedDate]) && Orders[ShippedDate] > Orders[RequiredDate]))` | #,0 |
| Waste Amount | Orders | `CALCULATE([Sales Amount], ISBLANK(Orders[ShippedDate]))` | $#,0 |
| Invoiced Amount | Invoices | `SUM(Invoices[ExtendedPrice])` | $#,0 |
| Invoiced Orders | Invoices | `DISTINCTCOUNT(Invoices[OrderID])` | #,0 |
| Orders Invoiced % | Invoices | `DIVIDE([Invoiced Orders], [Order Count])` | 0.0% |
| Order-Invoice Gap | Invoices | `[Sales Amount] - [Invoiced Amount]` | $#,0.00 |
| Discount % of Gross | Order_Details | `DIVIDE([Discount Amount], [Gross Sales])` | 0.0% |

> Caveat: `Orders[OrderID] → Invoices[OrderID]` has Orders on the many side; measure behavior must be validated when authoring.

## Narrative

Executives get three exception-oriented pages: (1) delivery performance with the 21 never-delivered orders called out as waste, (2) invoicing compliance showing full coverage and a rounding-level gap monitor, (3) discount evolution by month and country across 1996–1998.

---

## Canonical design contract

```yaml
Design Brief:
  generated_by: powerbi-report-design
  contract_version: 1
  mode: brownfield
  design_identity:
    tone: "Operational Monitor for executives — neutral light surface (#F3F2F1), Segoe UI, clean 8-px grid, semantic status colors (green=delivered/invoiced, amber=late, red=never delivered/waste), exception highlighting via conditional formatting"
    signature: "Status-colored KPI strip with exception callout: every page opens with a 4-tile KPI band where the exception tile (waste, gap, discount spike) carries the red/amber accent"
    current_tone: "indistinct — default themed pages, no status semantics"
    current_signature: "none"
  archetype: Operational
  color_map:
    - measure: Order_Details[Sales Amount]
      color: "#0072B2"
      tint: "#DEEFFF"
    - measure: Orders[Shipped Orders]
      color: "#009E73"
      tint: "#D9F2EA"
    - measure: Orders[Unshipped Orders]
      color: "#D55E00"
      tint: "#FBE4D5"
    - measure: Orders[Late Deliveries]
      color: "#E69F00"
      tint: "#FBEFD5"
    - measure: Invoices[Invoiced Amount]
      color: "#009E73"
      tint: "#D9F2EA"
    - measure: Invoices[Order-Invoice Gap]
      color: "#D55E00"
      tint: "#FBE4D5"
    - measure: Order_Details[Discount Amount]
      color: "#CC79A7"
      tint: "#F6E4EE"
  pages:
    - name: "809 of 830 Orders Delivered — 21 Never Shipped"
      role: detail
      archetype: Operational
      layout_variant: A
      variant_rationale: "Quadrant monitor fits four equal-weight delivery questions (by product, by city, by customer, waste detail) beneath a status KPI strip"
      page_background: "#F3F2F1"
      layout_summary: "KPI band (orders / delivered / never delivered / late), then quadrants: delivered by category, delivered by customer city, top-10 customers, never-delivered waste table"
      layout_contract:
        canvas: { width: 1280, height: 720, margin: 24, gutter: 16, snap: 8 }
        grid:
          columns: 12
          rows: 12
          regions:
            header:  [1, 1, 9, 2]
            filters: [9, 1, 13, 2]
            kpis:    [1, 2, 13, 4]
            tl:      [1, 4, 7, 8]
            tr:      [7, 4, 13, 8]
            bl:      [1, 8, 7, 13]
            br:      [7, 8, 13, 13]
        placements:
          - id: page_title
            region: header
            kind: textbox
            text: "809 of 830 Orders Delivered — 21 Never Shipped"
            purpose: "State delivery outcome and the waste exception up front"
          - id: year_slicer
            region: filters
            kind: slicer
            field_bindings: Orders[OrderDate]
            slicer_type: dropdown
            insight_basis: "Year-level filtering; executives compare 1996/1997/1998"
            slot: 1
            of: 2
          - id: city_slicer
            region: filters
            kind: slicer
            field_bindings: Customers[City]
            slicer_type: dropdown
            slot: 2
            of: 2
          - id: kpi_orders
            region: kpis
            kind: cardVisual
            purpose: "How many orders in scope?"
            field_bindings: Orders[Order Count]
            color_strategy: measure_match
            slot: 1
            of: 4
          - id: kpi_delivered
            region: kpis
            kind: cardVisual
            purpose: "How many orders delivered?"
            field_bindings: Orders[Shipped Orders]
            color_strategy: measure_match
            slot: 2
            of: 4
          - id: kpi_never_delivered
            region: kpis
            kind: cardVisual
            purpose: "How many orders never delivered (waste)?"
            field_bindings: Orders[Unshipped Orders]
            color_strategy: measure_match
            insight_basis: "Exception tile — red accent, the page's alert"
            slot: 3
            of: 4
          - id: kpi_late
            region: kpis
            kind: cardVisual
            purpose: "How many delivered late vs required date?"
            field_bindings: Orders[Late Deliveries]
            color_strategy: measure_match
            insight_basis: "Amber threshold tile — late-but-delivered exceptions"
            slot: 4
            of: 4
          - id: delivered_by_category
            region: tl
            kind: barChart
            purpose: "Which product categories account for delivered orders?"
            field_bindings: { Category: categories[CategoryName], Y: Orders[Shipped Orders] }
            sort_policy: value_desc
            color_strategy: measure_match
          - id: delivered_by_city
            region: tr
            kind: barChart
            purpose: "Which customer cities receive the most deliveries?"
            field_bindings: { Category: Customers[City], Y: Orders[Shipped Orders] }
            sort_policy: value_desc
            color_strategy: gradient
          - id: top_customers_delivered
            region: bl
            kind: barChart
            purpose: "Which top-10 customers receive the most delivered orders?"
            field_bindings: { Category: Customers[CompanyName], Y: Orders[Shipped Orders] }
            sort_policy: value_desc
            color_strategy: gradient
            comparison_basis: "Top N filter = 10 by Shipped Orders"
          - id: waste_detail
            region: br
            kind: tableEx
            purpose: "Which specific orders were never delivered, and what value is at risk?"
            field_bindings: [Orders[OrderID], Customers[CompanyName], Customers[City], Orders[OrderDate], Order_Details[Sales Amount]]
            sort_policy: value_desc
            insight_basis: "Filtered to ShippedDate blank — the 21 waste orders and their Waste Amount"
        space_audit:
          content_cell_count: 132
          placed_cell_count: 132
          empty_cell_pct: 0
          unplaced_regions: []
          largest_region: { name: bl, pct_of_content: 23 }
          balance_rationale: "KPI band answers 'is delivery on track'; four equal quadrants each answer one distinct delivery question; waste table gets the bottom-right exception slot"
    - name: "All 830 Orders Invoiced — Gap to Order Value Is Rounding-Level"
      role: detail
      archetype: Comparative
      layout_variant: B
      variant_rationale: "Coverage is 100%, so the page compares order vs invoiced amounts; hero ranking of top customers plus a reconciliation detail table"
      page_background: "#F3F2F1"
      layout_summary: "KPI band (invoiced orders / % invoiced / order amount / gap), hero: top-3 customers order vs invoiced clustered bar, right: gap by customer city, bottom: per-customer reconciliation table"
      layout_contract:
        canvas: { width: 1280, height: 720, margin: 24, gutter: 16, snap: 8 }
        grid:
          columns: 12
          rows: 12
          regions:
            header:  [1, 1, 9, 2]
            filters: [9, 1, 13, 2]
            kpis:    [1, 2, 13, 4]
            hero:    [1, 4, 7, 9]
            gapcity: [7, 4, 13, 9]
            detail:  [1, 9, 13, 13]
        placements:
          - id: page_title
            region: header
            kind: textbox
            text: "All 830 Orders Invoiced — Gap to Order Value Is Rounding-Level"
            purpose: "State invoicing compliance and reframe the gap finding honestly"
          - id: year_slicer
            region: filters
            kind: slicer
            field_bindings: Orders[OrderDate]
            slicer_type: dropdown
            slot: 1
            of: 2
          - id: city_slicer
            region: filters
            kind: slicer
            field_bindings: Customers[City]
            slicer_type: dropdown
            slot: 2
            of: 2
          - id: kpi_invoiced_orders
            region: kpis
            kind: cardVisual
            purpose: "How many orders are invoiced?"
            field_bindings: Invoices[Invoiced Orders]
            color_strategy: measure_match
            slot: 1
            of: 4
          - id: kpi_invoiced_pct
            region: kpis
            kind: cardVisual
            purpose: "What % of orders are invoiced (compliance)?"
            field_bindings: Invoices[Orders Invoiced %]
            color_strategy: measure_match
            insight_basis: "Compliance tile — green at 100%, turns red if any order lacks an invoice"
            slot: 2
            of: 4
          - id: kpi_order_amount
            region: kpis
            kind: cardVisual
            purpose: "What is total net order value?"
            field_bindings: Order_Details[Sales Amount]
            color_strategy: measure_match
            slot: 3
            of: 4
          - id: kpi_gap
            region: kpis
            kind: cardVisual
            purpose: "What is the order-vs-invoiced amount gap?"
            field_bindings: Invoices[Order-Invoice Gap]
            color_strategy: measure_match
            insight_basis: "Exception monitor — currently ~$0.02 (rounding); alerts if future data diverges"
            slot: 4
            of: 4
          - id: top3_customers
            region: hero
            kind: barChart
            purpose: "For the top-3 customers, how do order amount and invoiced amount compare?"
            field_bindings: { Category: Customers[CompanyName], Y: [Order_Details[Sales Amount], Invoices[Invoiced Amount]] }
            sort_policy: value_desc
            color_strategy: measure_match
            comparison_basis: "Top N filter = 3 by Sales Amount; clustered bars order vs invoiced"
          - id: gap_by_city
            region: gapcity
            kind: barChart
            purpose: "Which customer cities show any order-invoice gap?"
            field_bindings: { Category: Customers[City], Y: Invoices[Order-Invoice Gap] }
            sort_policy: value_desc
            color_strategy: measure_match
          - id: reconciliation_table
            region: detail
            kind: tableEx
            purpose: "Per customer: orders, order amount, invoiced amount, gap — which accounts need review?"
            field_bindings: [Customers[CompanyName], Customers[City], Orders[Order Count], Order_Details[Sales Amount], Invoices[Invoiced Amount], Invoices[Order-Invoice Gap]]
            sort_policy: value_desc
        space_audit:
          content_cell_count: 132
          placed_cell_count: 132
          empty_cell_pct: 0
          unplaced_regions: []
          largest_region: { name: detail, pct_of_content: 36 }
          balance_rationale: "Compliance KPI band, two balanced comparison panels (top-3 customers, gap by city), and a full-width reconciliation table for account-level review"
    - name: "Discounts Total $88.7K (6.5% of Gross) — Monthly Evolution by Country"
      role: detail
      archetype: Analytical
      layout_variant: A
      variant_rationale: "23 months × 21 countries needs a full-width monthly trend hero plus country ranking and a year×country matrix for the evolution question"
      page_background: "#F3F2F1"
      layout_summary: "KPI band (discount amount / % of gross / gross sales / units sold), full-width monthly discount trend line, bottom: discount by country bar + country×year matrix"
      layout_contract:
        canvas: { width: 1280, height: 720, margin: 24, gutter: 16, snap: 8 }
        grid:
          columns: 12
          rows: 12
          regions:
            header:  [1, 1, 9, 2]
            filters: [9, 1, 13, 2]
            kpis:    [1, 2, 13, 4]
            trend:   [1, 4, 13, 9]
            country: [1, 9, 7, 13]
            matrix:  [7, 9, 13, 13]
        placements:
          - id: page_title
            region: header
            kind: textbox
            text: "Discounts Total $88.7K (6.5% of Gross) — Monthly Evolution by Country"
            purpose: "Quantify discount magnitude and frame the evolution question"
          - id: year_slicer
            region: filters
            kind: slicer
            field_bindings: Orders[OrderDate]
            slicer_type: dropdown
            slot: 1
            of: 2
          - id: country_slicer
            region: filters
            kind: slicer
            field_bindings: Customers[Country]
            slicer_type: dropdown
            slot: 2
            of: 2
          - id: kpi_discount
            region: kpis
            kind: cardVisual
            purpose: "How much discount was given?"
            field_bindings: Order_Details[Discount Amount]
            color_strategy: measure_match
            slot: 1
            of: 4
          - id: kpi_discount_pct
            region: kpis
            kind: cardVisual
            purpose: "How much of gross sales is given away as discount?"
            field_bindings: Order_Details[Discount % of Gross]
            color_strategy: measure_match
            insight_basis: "Derived ratio — discount intensity, not a duplicate of the amount tile"
            slot: 2
            of: 4
          - id: kpi_gross
            region: kpis
            kind: cardVisual
            purpose: "What gross sales base do discounts apply to?"
            field_bindings: Order_Details[Gross Sales]
            color_strategy: measure_match
            slot: 3
            of: 4
          - id: kpi_net
            region: kpis
            kind: cardVisual
            purpose: "What is net sales after discount?"
            field_bindings: Order_Details[Sales Amount]
            color_strategy: measure_match
            slot: 4
            of: 4
          - id: discount_trend
            region: trend
            kind: lineChart
            purpose: "How did monthly discount amounts evolve throughout the years?"
            field_bindings: { Category: Orders[OrderDate] (Year-Month hierarchy), Y: Order_Details[Discount Amount] }
            color_strategy: measure_match
          - id: discount_by_country
            region: country
            kind: barChart
            purpose: "Which countries receive the most discount?"
            field_bindings: { Category: Customers[Country], Y: Order_Details[Discount Amount] }
            sort_policy: value_desc
            color_strategy: gradient
          - id: country_year_matrix
            region: matrix
            kind: pivotTable
            purpose: "How did each country's discount evolve year over year?"
            field_bindings: { Rows: Customers[Country], Columns: Orders[OrderDate] (Year), Values: Order_Details[Discount Amount] }
            sort_policy: value_desc
            color_strategy: gradient
            comparison_basis: "Year-over-year within country, background conditional formatting by Discount Amount"
        space_audit:
          content_cell_count: 132
          placed_cell_count: 132
          empty_cell_pct: 0
          unplaced_regions: []
          largest_region: { name: trend, pct_of_content: 45 }
          balance_rationale: "The monthly-evolution question is the page's core ask, so the trend line is the hero; country ranking and year matrix split the bottom to answer the per-country evolution"
  interaction_pattern:
    drill_targets: []
    cross_filter_rules: "Default cross-highlight within pages; slicers filter all visuals on their page"
  accessibility:
    alt_text_strategy: headline+trend
    contrast_notes: "Okabe-Ito colorblind-safe palette; status semantics (green/amber/red) always paired with labels, never color-only"
  theme:
    base: "assets/base.json adapted — status accents added; existing report theme (CY25SU12) preserved for existing pages"
    user_overrides: "Do not restyle the 4 existing pages"
```

---

## Data caveats (recorded for build & user awareness)

1. **All orders invoiced** — "% not invoiced" = 0%; page 2 is framed as a compliance/gap monitor rather than a not-invoiced breakdown.
2. **Total gap ≈ $0.02** — order vs invoiced amounts reconcile; the gap visuals act as exception detectors.
3. **Geography = Customers[City]** per user decision (ShipRegion is 61% blank).
4. **No Date table** — trends use `Orders[OrderDate]` hierarchy; a proper Date dimension is recommended future model work.
5. **Orders→Invoices relationship** has Orders on the many side — validate measure filter behavior during authoring.
6. Data covers **Jul 1996 – May 1998** only.

## Build sequence

1. Author 9 new measures in the ERP_Data semantic model (semantic-model-authoring skill).
2. Add 3 new pages to the PBIP per the design contract (powerbi-report-authoring skill); append to `pages.json` page order.
3. Validate PBIR, reload in Power BI Desktop, screenshot-verify each page.
