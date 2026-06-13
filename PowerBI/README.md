# Canadian Food Service Industry Analysis - Power BI Report

## Overview

This Power BI report takes the same dataset and methodology as the companion Excel workbook (see `/Excel` folder) and rebuilds the output layer for interactive exploration. It demonstrates capabilities - geographic visualization, cross-filtering, drill-through navigation - that a static Excel dashboard cannot replicate.

**Two pages:** a national overview with geographic filtering, and a province-level detail page accessible via drill-through.

📥 **Download the report:** `FoodService_Adjusted.pbix` (see Releases)

---

## Page 1 - Provincial Opportunity Map

Three KPI cards across the top show the national Recovery Index, the highest YoY growth province, and the lowest Recovery Index province at a glance.

A **region slicer** (West, Central, Prairies, Atlantic) filters all visuals on the page simultaneously - clicking Atlantic instantly narrows both the map and bar chart to New Brunswick, Nova Scotia, Newfoundland, and PEI only.

### Choropleth Map + Bar Chart - Two Visuals, One Story

The map and bar chart answer two different questions simultaneously:

- **The map answers "where"** - provinces shaded on a light-to-dark blue gradient by Recovery Index. Darker shading means stronger recovery relative to the 2019 baseline.
- **The bar chart answers "by how much"** - every province ranked by YoY Growth %, highest to lowest, with exact values labeled.

Neither visual tells the complete story alone. The map shows Atlantic provinces clustering at a similar recovery level but can't show their exact ranking. The bar chart shows New Brunswick and Nova Scotia tied at 7.0% but can't show their geographic relationship. Together, they make the regional pattern immediately readable — Atlantic Canada is both geographically concentrated and consistently outgrowing every large market.

Clicking any province on the map highlights its bar in the chart, and vice versa - every visual cross-filters every other visual simultaneously.

---

## Page 2 — Province Detail (Drill-Through)

Right-clicking any province on Page 1 and selecting **Drill Through** opens a dedicated detail page filtered to that province, showing:

- Annual sales trend, 2018-2025
- Service type breakdown for 2025

This gives full context behind the summary numbers on Page 1.

**Examples:**
- **Ontario** - largest market at $79.9B, steady recovery trend visible from the 2020 trough through 2025
- **New Brunswick** - fastest-growing province at +7.01% YoY, with limited-service eating places driving growth

---

## Data Model & DAX Measures

Built on the same star schema as the Excel workbook - `FoodService_Adjusted` (fact table) related to `Province_Lookup` (dimension table) via `Province_Code`.

| Measure | Purpose |
|---|---|
| **Total Sales** | Sums sales for the "Total, food services and drinking places" category, converted to actual dollars |
| **YoY Growth %** | Year-over-year percentage change in Total Sales |
| **Recovery Index** | Current year sales vs. 2019 baseline, indexed to 100 |

---

## Power BI Features Demonstrated

- **Shape Map** - Canada choropleth shaded by Recovery Index, using `CA-XX` province key format
- **Cross-filtering** - region slicer and all visuals filter each other simultaneously
- **Drill-through pages** - province-level detail accessible via right-click navigation
- **DAX measures** - Recovery Index, YoY Growth %, Sales aggregations with category exclusion logic
- **Data modeling** - star schema relationship between fact and dimension tables

---

## How to Use

1. Download `FoodService_Adjusted.pbix` from the Releases section of this repository
2. Open in Power BI Desktop (free)
3. On Page 1, use the region slicer to filter the map and bar chart
4. Right-click any province -> **Drill through -> Province Detail** to see that province's full trend and breakdown
5. Use the Back button on Page 2 to return to Page 1

---

## Related

This report is built on the same Statistics Canada dataset (Table 21-10-0019-01) and methodology as the companion Excel workbook - see the `/Excel` folder for the full data cleaning pipeline (Power Query), data model (Power Pivot), and DAX/XLOOKUP analysis.
