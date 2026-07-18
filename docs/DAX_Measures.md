# DAX Measures — Personal Care Supply Chain Dashboard

12 measures, grouped by purpose. All reference `Fact_SupplyChain` unless noted.

## Required KPIs (task spec)

```DAX
Total Revenue Generated = SUM(Fact_SupplyChain[Revenue generated])
```
Core revenue KPI — sum of revenue across all 100 SKUs.

```DAX
Total Products Sold = SUM(Fact_SupplyChain[Number of products sold])
```
Total sales volume in units, independent of price.

```DAX
Total Stocks = SUM(Fact_SupplyChain[Stock levels])
```
Current inventory across all SKUs — compare against Total Products Sold for a rough turnover read.

```DAX
Average Shipping Costs = AVERAGE(Fact_SupplyChain[Shipping costs])
```
Average cost to ship one SKU.

```DAX
Average Lead Time = AVERAGE(Fact_SupplyChain[Lead_Time_Procurement])
```
Average supplier/procurement lead time (the original dataset's `Lead time` column, renamed during
Power Query modeling to avoid confusion with the separate order-fulfillment lead time column).

## Supporting measures (added for chart coverage)

```DAX
Total Availability = SUM(Fact_SupplyChain[Availability])
```
Feeds the "Products Sold vs Availability" chart.

```DAX
Total Production Volume = SUM(Fact_SupplyChain[Production volumes])
```
Feeds the "Production Volumes by Product Type" chart.

```DAX
Avg Manufacturing Lead Time = AVERAGE(Fact_SupplyChain[Manufacturing lead time])
```
Manufacturing-side lead time, kept separate from procurement/fulfillment lead time for clarity.

```DAX
Avg Defect Rate % = AVERAGE(Fact_SupplyChain[Defect rates])
```
Quantifies quality risk alongside the categorical Inspection Result field.

```DAX
Pass Rate % =
DIVIDE(
    CALCULATE(COUNTROWS(Fact_SupplyChain), Dim_InspectionResult[Inspection results] = "Pass"),
    COUNTROWS(Fact_SupplyChain)
)
```
Share of SKUs with a confirmed quality pass (23% in the current dataset).

```DAX
Revenue per Unit Sold = DIVIDE([Total Revenue Generated], [Total Products Sold])
```
Price/revenue efficiency, useful for comparing locations or product types.

```DAX
Total Logistics Cost = SUM(Fact_SupplyChain[Costs])
```
General logistics cost estimate — the source dataset does not document this field's exact scope
(logistics-only vs. all-in cost), so it is treated as directional, not a defined P&L line.

## Modeling note — Count-based charts

Charts that group by a dimension (Transportation Mode, Inspection Result, Routes) should always use
**Count of a Fact table column** (e.g. `Fact_SupplyChain[SKU]`) for Values — never a column pulled
from the dimension table itself. Counting a dimension column returns the row count of that small
dimension table (3–4 rows), not the actual SKU distribution, and silently produces flat/misleading
results (e.g. an even split that isn't real).
