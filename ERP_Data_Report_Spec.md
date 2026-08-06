# ERP_Data Report Specification

Report name: ERP_Data  
Semantic model: ERP_Data  
Workspace: Retail CPG V2

## Purpose

ERP_Data should become a compact operational analytics report for retail and CPG users. The current report already proves that the model can answer product and discount questions, but it does not yet provide a complete business view. This specification turns the existing draft into a structured report that covers overview, product, customer, and order performance.

## Current Confirmed Model Coverage

The report files confirm the following model entities are available:

- Orders
- Order_Details
- Products
- Customers
- Invoices
- categories

The current visuals confirm these fields or expressions are already usable in the report:

- Products[ProductName]
- Order_Details[Discount Amount]
- Count(Customers)
- Count(Products)

Because the semantic model definition is not present in the workspace, this spec distinguishes between:

- Confirmed fields: already referenced by the PBIP report definition
- Candidate measures: strongly recommended but should be verified in the semantic model before implementation

## Design Principles

- Keep the report to 4 pages maximum so it remains easy to consume.
- Use the first page as an executive summary.
- Use consistent slicers across all pages where appropriate.
- Prefer measures over implicit aggregations.
- Avoid pages that only repeat the same metric in different visual forms.
- Ensure every page answers a distinct business question.

## Global Filters And Slicers

These slicers should appear on the Overview page and optionally be synced across the full report if the model supports them:

- Order Date or Invoice Date
- Product Category
- Product Name
- Customer Name
- Order Status or Invoice Status if available

If screen space is limited, place slicers in a left-side vertical pane or a top horizontal filter strip.

## Page 1: Executive Overview

Suggested page name: Executive Overview  
Current source: rename the existing "Page 1"

### Business Goal

Give a fast health check of the ERP business across sales, customers, products, and discount behavior.

### Core Questions Answered

- How is the business performing overall?
- Are sales concentrated in a few products or customers?
- Is discounting materially affecting performance?
- What changed over time?

### Recommended Layout

Top row:

- KPI card: Total Sales
- KPI card: Net Sales
- KPI card: Total Orders
- KPI card: Total Customers
- KPI card: Total Discount
- KPI card: Discount %

Middle row:

- Line chart: Sales trend by month
- Column or bar chart: Top 10 products by sales

Bottom row:

- Bar chart: Sales by category
- Table or matrix: Top customers with Sales, Orders, Discount

### Visual Specification

1. KPI card: Total Sales
   - Type: Card
   - Measure: candidate [Total Sales]
   - Purpose: top-line gross revenue

2. KPI card: Net Sales
   - Type: Card
   - Measure: candidate [Net Sales]
   - Purpose: revenue after discount

3. KPI card: Total Orders
   - Type: Card
   - Measure: candidate [Total Orders]
   - Purpose: order volume

4. KPI card: Total Customers
   - Type: Card
   - Measure: candidate [Total Customers]
   - Purpose: customer base size

5. KPI card: Total Discount
   - Type: Card
   - Measure: confirmed or existing Order_Details[Discount Amount] if it is already a measure
   - Purpose: total discount value

6. KPI card: Discount %
   - Type: Card
   - Measure: candidate [Discount %]
   - Purpose: show discount intensity relative to sales

7. Sales trend chart
   - Type: Line chart
   - Axis: candidate Date dimension or Orders/Invoices date field
   - Value: [Net Sales] or [Total Sales]
   - Purpose: monthly trend and seasonality

8. Top products chart
   - Type: Clustered bar chart
   - Axis: Products[ProductName]
   - Value: [Net Sales] preferred, fallback [Total Discount]
   - Sort: descending by value

9. Sales by category chart
   - Type: Column or bar chart
   - Axis: categories table field to be confirmed
   - Value: [Net Sales]

10. Top customers table
   - Type: Table or matrix
   - Rows: customer field to be confirmed
   - Values: [Net Sales], [Total Orders], [Total Discount]

### Notes

- The current product discount bar chart from the draft page can be reused here temporarily until Total Sales and Net Sales are defined.
- Replace generic implicit counts with explicit measures once the semantic model is available.

## Page 2: Product Order Analysis

Suggested page name: Product Order Analysis  
Current source: refine the existing page "Product Order Analysis"

### Business Goal

Show which products drive orders, sales, and discounts, and identify where discounting may be too aggressive.

### Core Questions Answered

- Which products contribute most to revenue?
- Which products receive the highest discounts?
- Which products have poor margin proxies due to discounting?
- Are there products with high discount but low order contribution?

### Existing Assets On This Page

Confirmed current visuals:

- Product count card
- Product vs Discount bar chart
- Product and Discount table

These visuals are a valid starting point, but the page needs broader product metrics.

### Recommended Layout

Left rail:

- KPI card: Total Products
- KPI card: Total Product Sales
- KPI card: Avg Discount per Product

Main canvas:

- Bar chart: Top products by Net Sales
- Scatter or combo chart: Product Sales vs Discount %
- Table: Product detail breakdown

### Visual Specification

1. KPI card: Total Products
   - Type: Card
   - Measure: candidate [Total Products]
   - Current fallback: Count(Products)

2. KPI card: Product Sales
   - Type: Card
   - Measure: candidate [Net Sales]
   - Filter context: full page product context

3. KPI card: Average Discount per Product
   - Type: Card
   - Measure: candidate [Avg Discount per Product]

4. Top products by sales
   - Type: Clustered bar chart
   - Axis: Products[ProductName]
   - Value: [Net Sales]
   - Top N filter: 10 or 15

5. Product discount chart
   - Type: Clustered bar chart or combo chart
   - Axis: Products[ProductName]
   - Value: [Total Discount]
   - Secondary value if possible: [Discount %]
   - This can evolve from the current existing bar chart.

6. Product detail table
   - Type: Table
   - Rows: Products[ProductName]
   - Values:
     - [Total Quantity]
     - [Total Sales]
     - [Total Discount]
     - [Discount %]
     - [Total Orders] or product order count if supported

7. Optional scatter plot
   - Type: Scatter chart
   - Details: Products[ProductName]
   - X-axis: [Net Sales]
   - Y-axis: [Discount %]
   - Size: [Total Quantity]
   - Purpose: identify products with weak discount efficiency

### Notes

- The current page is too focused on one metric. It should evolve into a product performance page, not just a discount page.
- If category fields are available, add a category slicer local to this page.

## Page 3: Customer Analysis

Suggested page name: Customer Analysis

### Business Goal

Show how demand is distributed across customers and which customers account for the most sales, orders, and discount exposure.

### Core Questions Answered

- Who are the highest value customers?
- Which customers receive the most discounts?
- Is revenue concentrated among a few accounts?
- How many orders does each customer generate?

### Recommended Layout

Top row:

- KPI card: Total Customers
- KPI card: Sales per Customer
- KPI card: Avg Orders per Customer

Main canvas:

- Bar chart: Top customers by Net Sales
- Bar chart: Top customers by Discount Amount
- Matrix: Customer detail summary

### Visual Specification

1. KPI card: Total Customers
   - Type: Card
   - Measure: candidate [Total Customers]
   - Current fallback: Count(Customers)

2. KPI card: Sales per Customer
   - Type: Card
   - Measure: candidate [Sales per Customer]

3. KPI card: Avg Orders per Customer
   - Type: Card
   - Measure: candidate [Avg Orders per Customer]

4. Top customers by sales
   - Type: Bar chart
   - Axis: customer name field to be confirmed
   - Value: [Net Sales]

5. Top customers by discount
   - Type: Bar chart
   - Axis: customer name field to be confirmed
   - Value: [Total Discount]

6. Customer summary matrix
   - Type: Matrix
   - Rows: customer name field
   - Values:
     - [Total Orders]
     - [Net Sales]
     - [Total Discount]
     - [Discount %]

### Notes

- This page depends on a valid relationship path from Customers to Orders and Order_Details.
- If geography exists in Customers, add a regional customer view later.

## Page 4: Orders And Invoices

Suggested page name: Orders and Invoices

### Business Goal

Provide operational visibility into order volume and invoice activity.

### Core Questions Answered

- How many orders and invoices are being processed?
- How do invoice totals compare over time?
- Are there operational spikes or slow periods?
- Which orders or invoices contribute the most value?

### Recommended Layout

Top row:

- KPI card: Total Orders
- KPI card: Total Invoices
- KPI card: Average Order Value
- KPI card: Average Invoice Value

Main canvas:

- Line chart: Orders over time
- Line or column chart: Invoices over time
- Table: order or invoice detail listing

### Visual Specification

1. KPI card: Total Orders
   - Type: Card
   - Measure: candidate [Total Orders]

2. KPI card: Total Invoices
   - Type: Card
   - Measure: candidate [Total Invoices]

3. KPI card: Average Order Value
   - Type: Card
   - Measure: candidate [Average Order Value]

4. KPI card: Average Invoice Value
   - Type: Card
   - Measure: candidate [Average Invoice Value]

5. Orders trend chart
   - Type: Line chart
   - Axis: Orders date field to be confirmed
   - Value: [Total Orders]

6. Invoices trend chart
   - Type: Line or clustered column chart
   - Axis: Invoices date field to be confirmed
   - Value: [Total Invoices] or invoice amount measure

7. Transaction detail table
   - Type: Table
   - Rows: Order ID or Invoice ID
   - Values: customer, product summary, amount, discount, date, status if available

### Notes

- This page should only be implemented if Orders and Invoices contain usable date and identifier fields in the model.
- If Invoices is sparse or redundant, this page can be simplified to Orders only.

## Measure Specification

### Confirmed Existing Measure Or Metric Usage

- Order_Details[Discount Amount] is already being used in current visuals and should remain part of the semantic layer.

### Candidate Measures To Add To The Semantic Model

These should be implemented in the ERP_Data semantic model if they do not already exist:

- Total Sales
- Net Sales
- Total Discount
- Discount %
- Total Orders
- Total Customers
- Total Products
- Total Quantity
- Sales per Customer
- Avg Orders per Customer
- Average Order Value
- Total Invoices
- Average Invoice Value
- Avg Discount per Product

### Candidate DAX Patterns

These formulas assume common ERP naming and should be adjusted to the actual column names in the semantic model.

```DAX
Total Discount =
SUM(Order_Details[Discount Amount])

Total Quantity =
SUM(Order_Details[Quantity])

Total Sales =
SUMX(
    Order_Details,
    Order_Details[Quantity] * Order_Details[UnitPrice]
)

Net Sales =
[Total Sales] - [Total Discount]

Discount % =
DIVIDE([Total Discount], [Total Sales], 0)

Total Orders =
DISTINCTCOUNT(Orders[OrderID])

Total Customers =
DISTINCTCOUNT(Customers[CustomerID])

Total Products =
DISTINCTCOUNT(Products[ProductID])

Sales per Customer =
DIVIDE([Net Sales], [Total Customers], 0)

Avg Orders per Customer =
DIVIDE([Total Orders], [Total Customers], 0)

Average Order Value =
DIVIDE([Net Sales], [Total Orders], 0)
```

## Prioritized Build Sequence

Implement the report in this order:

1. Rename and rebuild Page 1 as Executive Overview.
2. Upgrade Product Order Analysis to use Sales and Discount together.
3. Add explicit measures to replace implicit counts.
4. Add Customer Analysis once customer keys and names are verified.
5. Add Orders and Invoices page once date and amount columns are verified.

## Implementation Risks

- The semantic model files are not present in the workspace, so some proposed measures depend on field names not yet verified.
- The categories table exists in the diagram, but no category field is referenced yet in the report definition.
- Invoice field availability is unknown from the checked-in files.
- Date fields are not confirmed in the current PBIP artifacts.

## Minimum Viable Report

If only a minimal first release is needed, build these pages first:

- Executive Overview
- Product Order Analysis

Use these confirmed or low-risk metrics first:

- Discount Amount
- Count of Products
- Count of Customers
- Product Name

Then extend the report after the semantic model is available for direct inspection.