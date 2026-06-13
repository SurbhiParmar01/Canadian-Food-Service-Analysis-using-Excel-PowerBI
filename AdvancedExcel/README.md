# Canadian Food Service Industry Analysis - Excel Workbook

## Overview

This workbook analyzes Canada's food service industry using official Statistics Canada data (Table 21-10-0019-01, Monthly Survey of Food Services and Drinking Places) covering January 2018 to December 2025. It answers four business questions a regional restaurant chain's strategy team would care about: COVID recovery by province, seasonal staffing patterns, market structure shifts, and a provincial opportunity matrix for expansion decisions.

**Key finding:** National Recovery Index is 131.7 in 2025 (31.7% above pre-COVID levels). New Brunswick leads YoY growth at +7.01%, and Atlantic Canada is quietly outgrowing every large market.

---

## Dashboard

![Excel Dashboard - Canadian Food Service Analysis](Dashboard_Preview.jpg)

## Data Source

Statistics Canada, Table 21-10-0019-01 - Monthly Survey of Food Services and Drinking Places. Seasonally adjusted monthly revenue data, freely available as CSV download.

Before importing, the STATUS column was reviewed per Statistics Canada documentation to understand suppression flags. Suppressed values (marked `x` or `F`) were converted to nulls in the VALUE column - removing those rows entirely would have silently distorted provincial totals.

---

## Workbook Structure

| Sheet | Purpose |
|---|---|
| `Raw_Data` | Original Statistics Canada CSV import |
| `FoodService_Adjusted` | Cleaned data after Power Query transformation (connection only, loaded to Data Model) |
| `Province_Lookup` | Province metadata - Region, Population_Group |
| `Province_Summary` | XLOOKUP, SUMIFS, and nested IF demonstrations |
| `Analysis 1` | COVID Crash & Provincial Recovery - Recovery Index heatmap |
| `Analysis 2` | Seasonal Patterns by Service Type - line chart |
| `Analysis 3` | Full-Service vs Limited-Service Market Share - stacked bar |
| `Analysis 4` | Provincial Market Opportunity Matrix - bubble chart |
| `Dashboard` | Interactive dashboard with cross-connected slicer |

---

## Methodology

### 1. Power Query - Data Cleaning & Transformation

11 documented transformation steps, fully visible in the Applied Steps panel for auditability:

- Filtered data to 2018 onwards using Date Filters
- Removed unnecessary columns (DGUID, VECTOR, SCALAR_FACTOR, UOM, STATUS, SYMBOL, TERMINATED, DECIMALS) - each reviewed against metadata documentation before removal
- Extracted Year, Month Number, and Month-Year label using `Date.Year()`, `Date.Month()`, `Date.ToText()`
- Changed VALUE column to Decimal Number, leaving suppressed nulls in place
- Added `Province_Code` custom column; filtered out NWT, Yukon, and Nunavut (sparse/suppressed data)
- Renamed columns to descriptive names: `Sales_Thousands`, `Service_Type`, `Province_Code`
- Loaded as connection only - added to Data Model

### 2. Province Lookup Table - Data Enrichment

A manually built `Province_Lookup` table enriches the raw data with **Region** (West, Central, Prairies, Atlantic) and **Population_Group** (Large, Medium, Small) classifications not present in the original dataset. This enables the Region slicer on the dashboard and the XLOOKUP demonstrations.

### 3. Power Pivot Data Model - Star Schema

`FoodService_Adjusted` (fact table) connects to `Province_Lookup` (dimension table) via `Province_Code` in a many-to-one relationship - the standard star schema structure used in professional BI tools, including Power BI.

### 4. DAX Measures

| Measure | Purpose |
|---|---|
| **Total Sales** | Sums `Sales_Thousands` filtered to "Total, food services and drinking places," multiplied by 1,000 - avoids double-counting from subcategory rows |
| **YoY Growth %** | Uses `CALCULATE` with `SAMEPERIODLASTYEAR` to shift context back 12 months; `DIVIDE` returns blank instead of error when denominator is zero |
| **vs 2019 Baseline %** | Uses `VAR` to store the 2019 baseline via `ALLEXCEPT`, preserving province filter while replacing year filter - compares each province to its own 2019 performance |
| **Recovery Index** | Same logic as Baseline %, returns a score (100 = fully recovered, below 100 = lagging, above 100 = exceeded pre-COVID) |

### 5. XLOOKUP — Basic to Multi-Condition

**Basic XLOOKUP** - pulls Region and Population_Group via structured references:
```excel
=XLOOKUP(A2, Province_Lookup[Province_Code], Province_Lookup[Region], "Not Found")
```

**Multi-condition XLOOKUP** - composite key lookup across Province, Year, Service Type:
```excel
=XLOOKUP(A2&"2024"&"Total, food services and drinking places", 
FoodService_Adjusted[Province_Code]&FoodService_Adjusted[Year]&FoodService_Adjusted[Service_Type], 
FoodService_Adjusted[Sales_Thousands], "No data")*1000
```

**Formula Validation - XLOOKUP corrected to SUMIFS**

The initial multi-condition XLOOKUP returned only the first matching row - since the data is monthly, it returned one month's value instead of the annual total. This was caught by cross-checking against DAX measure totals and corrected with SUMIFS, which aggregates all 12 monthly values:
```excel
=SUMIFS(FoodService_Adjusted[Sales_Thousands], FoodService_Adjusted[GEO], A2, 
FoodService_Adjusted[Year], 2025, 
FoodService_Adjusted[Service_Type], "Total, food services and drinking places") * 1000
```

**Nested IF - Market Tier Classification**

Classifies each province by vs-2019 growth tier, with thresholds based on actual data distribution (20%–46% range):
```excel
=IF(G2>0.35,"Exceptional Growth",IF(G2>0.25,"Strong Growth","Moderate Growth"))
```
Conditional formatting applied: green (Exceptional, >35%), light green (Strong, 25–35%), yellow (Moderate, <25%).

---

## The Four Analyses

1. **COVID Crash & Provincial Recovery** - Recovery Index heatmap. 2020 lows (Ontario 69.0, Quebec 68.6); 2025 leaders (PEI 145.9, Nova Scotia 137.5).
2. **Seasonal Patterns by Service Type** - Full-service restaurants peak July–August; limited-service stays flat year-round; special food services spike in September.
3. **Market Share Shift** - Full-service vs limited-service split remained within 2–3 points of pre-COVID levels, contradicting predictions of a permanent shift to fast food.
4. **Provincial Opportunity Matrix** - Bubble chart (market size × growth × Recovery Index) identifies Ontario as the dominant expansion market and Atlantic Canada/Saskatchewan as emerging high-growth opportunities.

---

## Key Skills Demonstrated

Power Query (multi-step transformation pipeline), Power Pivot (star schema data model), DAX (`CALCULATE`, `ALLEXCEPT`, `SAMEPERIODLASTYEAR`, `VAR`), XLOOKUP (basic and multi-condition composite key), structured table references, Recovery Index design, quadrant analysis framework, interactive dashboard with cross-connected slicer, data enrichment via lookup table.

---

## Related

This analysis is also rebuilt in Power BI with an interactive choropleth map, cross-filtering slicers, and drill-through province detail pages - see the `/PowerBI` folder in this repository.

---

## Author

**Surbhi Parmar**
Portfolio: [surbhiparmar01.github.io](https://surbhiparmar01.github.io/SurbhiParmarPortfolio/)
LinkedIn: [linkedin.com/in/surbhiparmar](https://www.linkedin.com/in/surbhiparmar/)
GitHub: [github.com/SurbhiParmar01](https://github.com/SurbhiParmar01)

## Data Source

Statistics Canada, Table 21-10-0019-01 - Monthly Survey of Food Services and Drinking Places
