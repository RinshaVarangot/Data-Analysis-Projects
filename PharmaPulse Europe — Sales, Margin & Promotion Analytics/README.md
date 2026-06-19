
# Margin & Promotion Analytics

A 4-page Power BI dashboard analyzing sales, profitability, and promotional performance for a pharmacy distributor operating across 8 European countries.

Built on a star-schema data model (1 fact table, 3 dimension tables) covering two full fiscal years (2024–2025), ~62,000 transactions, and 5 product categories.


## 📊 Dashboard Pages

### 1. Executive Overview

High-level KPIs and trend summary for leadership.


KPI cards: Total Revenue, Total Margin, Margin %, Total Units Sold
Revenue trend line (monthly, with YoY context)
Revenue by Category (donut chart)
Country-level map view
Year / Quarter slicers (synced across all pages)


### 2. Pharmacy Performance

Regional and store-level breakdown of the distribution network.


Map of all pharmacy locations, sized by revenue
Country → Region → City → Pharmacy drill-down matrix (Total Revenue, Total Margin, Margin %, Active Pharmacy Count)
Clustered bar charts: Revenue by Pharmacy Type (Urban/Suburban/Rural) and Store Size Band (S/M/L)
KPI cards: Active Pharmacy Count, Revenue per Pharmacy, Total Revenue


### 3. Product Performance

Category and SKU-level profitability analysis.


Revenue by Category → Brand (treemap, drill-down)
Top 15 Products by Revenue (table: Units Sold, Margin %, Generic/Branded flag)
Generic vs. Branded revenue split (card pair)
Price vs. Margin scatter plot, bubble-sized by units sold — surfaces pricing outliers
KPI cards: Total Revenue, Active Products Sold, Margin %, Avg Revenue per Transaction


### 4. Promotions & Trends

Impact of promotional activity on revenue.


Promo vs. Non-Promo revenue by month (clustered column chart)
Revenue YoY % trend line, with a 0% baseline reference
Category-level Promo vs. Non-Promo revenue breakdown (table)
KPI cards: Promo Revenue %, Promo Revenue, Non-Promo Revenue



## 🗂️ Data Model

Star schema with one fact table and three dimension tables, all single-direction relationships (dimension → fact):

TableDescriptionFactSales~62,000 transaction-level sales records: units, revenue, cost, margin, promo flagDimDateCalendar table (2024–2025), marked as the model's official date tableDimPharmacyPharmacy locations: country, region, city, type, size band, open date, coordinatesDimProductProduct catalog: category, brand, generic/branded flag, list price, discontinued status

## Hierarchies:


Geography: Country → Region → City → Pharmacy Name

Product: Category → Brand → Product Name

Time: Year → Quarter → Month → Date



## 🧮 Key DAX Measures

daxTotal Revenue = SUM(FactSales[RevenueEUR])
Total Margin = SUM(FactSales[MarginEUR])
Margin % = DIVIDE([Total Margin], [Total Revenue], 0)
Revenue YoY % = DIVIDE([Total Revenue] - [Revenue PY], [Revenue PY], 0)
Promo Revenue % = DIVIDE([Promo Revenue], [Total Revenue], 0)
Generic Revenue % = DIVIDE([Generic Revenue], [Total Revenue], 0)
Active Pharmacy Count = DISTINCTCOUNT(FactSales[PharmacyID])
Revenue per Pharmacy = DIVIDE([Total Revenue], [Active Pharmacy Count], 0)

20+ measures in total, covering revenue/margin, time intelligence (YTD/MTD/YoY/MoM), promotions, generics vs. branded, and network performance. Full list in /docs (optional — add if you export one).


## 🛠️ Tools & Skills Used


Power BI Desktop — data modeling, DAX, report design
Star schema modeling — fact/dimension design, single-direction relationships
Time intelligence DAX — SAMEPERIODLASTYEAR, TOTALYTD, DATEADD
Drill-down hierarchies — Geography, Product, Time
Custom matrix conditional formatting — data bars, background color scales


##📌 Notes


Some pharmacies and products have open/discontinued dates — zero-sales periods before opening or after discontinuation are expected, not data errors.
MarginEUR is pre-calculated in the source data as RevenueEUR − CostEUR.
Geographic coordinates are slightly jittered for map readability.



Built as a portfolio project to demonstrate Power BI data modeling, DAX, and dashboard design for a multi-country retail/distribution scenario.

