# ERP_Data Implementation Checklist

Report name: ERP_Data  
Semantic model: ERP_Data  
Workspace: Retail CPG V2

## Current Status

This checklist now reflects the actual state of the workspace.

- `[x]` means the item is confirmed in the checked-in PBIP report files.
- `[ ]` means the item is not yet implemented or cannot be verified from the workspace.
- Items remain unchecked when the semantic model or report artifacts needed to prove completion are not present.

## Pre-Build Validation

- [ ] Confirm the semantic model ERP_Data is connected to the report.
- [ ] Confirm the workspace is Retail CPG V2.
- [x] Verify model relationships between Customers, Orders, Order_Details, Products, Invoices, and categories.
- [ ] Confirm whether Order_Details[Discount Amount] is a true measure or a numeric column used in a measure slot.
- [ ] Verify key columns for Orders, Customers, Products, and Invoices.
- [ ] Verify at least one usable date field for time-based visuals.
- [ ] Verify at least one category field from the categories table.
- [ ] Replace implicit counts with explicit measures where possible.

## Semantic Model Measures

### Confirmed Or Existing Metric Usage

- [x] Validate existing use of Order_Details[Discount Amount].

### Candidate Measures To Add Or Verify

- [ ] Total Sales
- [ ] Net Sales
- [ ] Total Discount
- [ ] Discount %
- [ ] Total Orders
- [ ] Total Customers
- [ ] Total Products
- [ ] Total Quantity
- [ ] Sales per Customer
- [ ] Avg Orders per Customer
- [ ] Average Order Value
- [ ] Total Invoices
- [ ] Average Invoice Value
- [ ] Avg Discount per Product

## Page 1: Executive Overview

Target page name: Executive Overview  
Source page: current Page 1

### Page Setup

- [x] Rename Page 1 to Executive Overview.
- [ ] Add page title text.
- [ ] Add a subtitle or helper text describing the page purpose.
- [ ] Apply final page layout and spacing.
- [ ] Add synced slicers if supported by the model.

### Filters And Slicers

- [ ] Add Date slicer.
- [ ] Add Product Category slicer.
- [ ] Add Product Name slicer.
- [ ] Add Customer slicer.
- [ ] Add Order or Invoice Status slicer if available.

### KPI Row

#### Visual: Total Sales Card

- [ ] Add card visual.
- [ ] Bind to [Total Sales].
- [ ] Format currency.
- [ ] Add title: Total Sales.
- [ ] Validate value against model totals.

#### Visual: Net Sales Card

- [ ] Add card visual.
- [ ] Bind to [Net Sales].
- [ ] Format currency.
- [ ] Add title: Net Sales.
- [ ] Validate value against model totals.

#### Visual: Total Orders Card

- [ ] Add card visual.
- [ ] Bind to [Total Orders].
- [ ] Format whole number.
- [ ] Add title: Total Orders.
- [ ] Validate value against model totals.

#### Visual: Total Customers Card

- [x] Add card visual.
- [ ] Bind to [Total Customers].
- [x] Format whole number.
- [ ] Add title: Total Customers.
- [ ] Validate value against model totals.

#### Visual: Total Discount Card

- [x] Add card visual.
- [x] Bind to [Total Discount] or current Discount Amount metric.
- [ ] Format currency.
- [ ] Add title: Total Discount.
- [ ] Validate value against model totals.

#### Visual: Discount % Card

- [ ] Add card visual.
- [ ] Bind to [Discount %].
- [ ] Format as percentage.
- [ ] Add title: Discount %.
- [ ] Validate value against model totals.

### Trend And Ranking Visuals

#### Visual: Sales Trend By Month

- [ ] Add line chart.
- [ ] Bind axis to verified date field.
- [ ] Bind values to [Net Sales] or [Total Sales].
- [ ] Set month-level trend granularity.
- [ ] Sort chronologically.
- [ ] Add title: Sales Trend.
- [ ] Validate continuity and date coverage.

#### Visual: Top Products By Sales

- [x] Add clustered bar chart.
- [x] Bind axis to Products[ProductName].
- [ ] Bind value to [Net Sales].
- [ ] Apply Top N filter.
- [x] Sort descending.
- [ ] Add title: Top Products by Sales.
- [ ] Validate product ranking.

#### Visual: Sales By Category

- [ ] Add bar or column chart.
- [ ] Bind axis to verified category field.
- [ ] Bind value to [Net Sales].
- [ ] Sort descending.
- [ ] Add title: Sales by Category.
- [ ] Validate category aggregation.

#### Visual: Top Customers Summary

- [ ] Add table or matrix.
- [ ] Bind row field to verified customer name field.
- [ ] Add [Net Sales].
- [ ] Add [Total Orders].
- [ ] Add [Total Discount].
- [ ] Sort by [Net Sales] descending.
- [ ] Add title: Top Customers.
- [ ] Validate customer totals.

### Final Validation For Executive Overview

- [ ] Validate all cards respond correctly to slicers.
- [ ] Validate all charts cross-filter correctly.
- [ ] Remove or replace the current draft visuals no longer needed.
- [x] Confirm page readability at report default size.

## Page 2: Product Order Analysis

Target page name: Product Order Analysis  
Source page: existing Product Order Analysis

### Page Setup

- [x] Keep or refine the existing page name.
- [ ] Add page title text.
- [x] Align left rail KPI cards.
- [x] Resize visuals to avoid unused space.
- [ ] Add local product or category slicer if needed.

### Existing Visual Refactor

#### Existing Visual: Product Count Card

- [x] Review existing visual.
- [ ] Replace implicit count with [Total Products] if available.
- [ ] Confirm title is Product Count or Total Products.
- [ ] Validate count logic.

#### Existing Visual: Product Discount Bar Chart

- [x] Review existing visual.
- [x] Decide whether to keep as Discount view or convert to Sales view.
- [ ] If converted, bind to [Net Sales].
- [x] If kept, bind to [Total Discount].
- [x] Confirm descending sort.
- [ ] Add final title.

#### Existing Visual: Product Detail Table

- [x] Review existing visual.
- [x] Keep Products[ProductName].
- [ ] Add [Total Quantity].
- [ ] Add [Total Sales].
- [x] Add [Total Discount].
- [ ] Add [Discount %].
- [ ] Remove low-value fields if cluttered.
- [ ] Validate totals.

### New KPI Visuals

#### Visual: Product Sales Card

- [ ] Add card visual.
- [ ] Bind to [Net Sales].
- [ ] Format currency.
- [ ] Add title: Product Sales.
- [ ] Validate value in page context.

#### Visual: Average Discount Per Product Card

- [ ] Add card visual.
- [ ] Bind to [Avg Discount per Product].
- [ ] Format currency or percentage as designed.
- [ ] Add title: Avg Discount per Product.
- [ ] Validate value in page context.

### New Analytical Visuals

#### Visual: Top Products By Net Sales

- [ ] Add clustered bar chart.
- [ ] Bind axis to Products[ProductName].
- [ ] Bind value to [Net Sales].
- [ ] Apply Top N filter.
- [ ] Sort descending.
- [ ] Add title: Top Products by Net Sales.
- [ ] Validate ranking.

#### Visual: Product Sales Vs Discount Efficiency

- [ ] Add combo chart or scatter chart.
- [ ] Bind Products[ProductName] as detail or axis.
- [ ] Bind [Net Sales].
- [ ] Bind [Discount %] or [Total Discount].
- [ ] Add title describing comparison.
- [ ] Validate interpretation.

### Final Validation For Product Order Analysis

- [ ] Validate all visuals respond correctly to slicers.
- [ ] Confirm no duplicate visuals remain without purpose.
- [ ] Ensure the page answers both sales and discount questions.

## Page 3: Customer Analysis

Target page name: Customer Analysis

### Page Setup

- [x] Create new page.
- [ ] Add page title.
- [ ] Add synced slicers as needed.
- [ ] Confirm customer field naming.

### KPI Row

#### Visual: Total Customers Card

- [ ] Add card visual.
- [ ] Bind to [Total Customers].
- [ ] Format whole number.
- [ ] Add title: Total Customers.
- [ ] Validate value.

#### Visual: Sales Per Customer Card

- [ ] Add card visual.
- [ ] Bind to [Sales per Customer].
- [ ] Format currency.
- [ ] Add title: Sales per Customer.
- [ ] Validate value.

#### Visual: Avg Orders Per Customer Card

- [ ] Add card visual.
- [ ] Bind to [Avg Orders per Customer].
- [ ] Format decimal or whole number as needed.
- [ ] Add title: Avg Orders per Customer.
- [ ] Validate value.

### Main Visuals

#### Visual: Top Customers By Sales

- [ ] Add bar chart.
- [ ] Bind axis to verified customer name field.
- [ ] Bind values to [Net Sales].
- [ ] Apply Top N filter.
- [ ] Sort descending.
- [ ] Add title: Top Customers by Sales.
- [ ] Validate ranking.

#### Visual: Top Customers By Discount

- [ ] Add bar chart.
- [ ] Bind axis to verified customer name field.
- [ ] Bind values to [Total Discount].
- [ ] Apply Top N filter.
- [ ] Sort descending.
- [ ] Add title: Top Customers by Discount.
- [ ] Validate ranking.

#### Visual: Customer Summary Matrix

- [ ] Add matrix visual.
- [ ] Bind rows to verified customer name field.
- [ ] Add [Total Orders].
- [ ] Add [Net Sales].
- [ ] Add [Total Discount].
- [ ] Add [Discount %].
- [ ] Sort by [Net Sales].
- [ ] Add title: Customer Summary.
- [ ] Validate totals.

### Final Validation For Customer Analysis

- [ ] Confirm Customers filters Orders and Order_Details correctly.
- [ ] Validate totals against the overview page.
- [ ] Confirm page remains usable with high customer counts.

## Page 4: Orders And Invoices

Target page name: Orders and Invoices

### Page Setup

- [x] Create new page.
- [ ] Add page title.
- [ ] Confirm date fields and transaction identifiers are available.
- [ ] Confirm whether this page should include both Orders and Invoices or Orders only.

### KPI Row

#### Visual: Total Orders Card

- [ ] Add card visual.
- [ ] Bind to [Total Orders].
- [ ] Format whole number.
- [ ] Add title: Total Orders.
- [ ] Validate value.

#### Visual: Total Invoices Card

- [ ] Add card visual.
- [ ] Bind to [Total Invoices].
- [ ] Format whole number.
- [ ] Add title: Total Invoices.
- [ ] Validate value.

#### Visual: Average Order Value Card

- [ ] Add card visual.
- [ ] Bind to [Average Order Value].
- [ ] Format currency.
- [ ] Add title: Average Order Value.
- [ ] Validate value.

#### Visual: Average Invoice Value Card

- [ ] Add card visual.
- [ ] Bind to [Average Invoice Value].
- [ ] Format currency.
- [ ] Add title: Average Invoice Value.
- [ ] Validate value.

### Main Visuals

#### Visual: Orders Trend

- [ ] Add line chart.
- [ ] Bind axis to verified order date field.
- [ ] Bind value to [Total Orders].
- [ ] Sort chronologically.
- [ ] Add title: Orders Trend.
- [ ] Validate trend.

#### Visual: Invoices Trend

- [ ] Add line or clustered column chart.
- [ ] Bind axis to verified invoice date field.
- [ ] Bind value to [Total Invoices] or invoice amount measure.
- [ ] Sort chronologically.
- [ ] Add title: Invoices Trend.
- [ ] Validate trend.

#### Visual: Transaction Detail Table

- [ ] Add table visual.
- [ ] Bind Order ID or Invoice ID.
- [ ] Bind customer name field.
- [ ] Bind date field.
- [ ] Bind amount measure.
- [ ] Bind discount measure if relevant.
- [ ] Bind status if available.
- [ ] Add title: Transaction Detail.
- [ ] Validate totals and drill usefulness.

### Final Validation For Orders And Invoices

- [ ] Confirm order and invoice counts reconcile with the model.
- [ ] Confirm date filters affect all visuals correctly.
- [ ] Remove the invoice section if the table is not analysis-ready.

## Cross-Page Formatting And UX

- [x] Apply consistent theme and typography.
- [ ] Apply consistent card sizes and alignment.
- [ ] Use consistent number formatting across all pages.
- [ ] Use consistent page titles and subtitles.
- [ ] Use consistent chart titles and legend naming.
- [ ] Confirm all interactions are intentional.
- [ ] Disable distracting or low-value interactions.
- [ ] Add tooltips where useful.

## Final QA Checklist

- [ ] Validate every page with no slicers applied.
- [ ] Validate every page with each major slicer applied.
- [ ] Validate top totals reconcile across pages.
- [ ] Validate no visual errors appear.
- [ ] Validate there are no blank charts due to missing relationships.
- [ ] Validate performance is acceptable when filtering.
- [ ] Confirm all draft visuals have been renamed logically.
- [x] Confirm page order is final.
- [x] Confirm the active landing page is Executive Overview.
- [ ] Publish and smoke test in Retail CPG V2.

## Suggested Delivery Order

- [ ] Complete Semantic Model Measures.
- [ ] Complete Executive Overview.
- [ ] Complete Product Order Analysis.
- [x] Complete Customer Analysis.
- [x] Complete Orders and Invoices.
- [ ] Complete final QA.