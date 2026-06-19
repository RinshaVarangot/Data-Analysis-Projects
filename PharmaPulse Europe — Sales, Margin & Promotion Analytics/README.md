# PharmaPulse Europe — Sales, Margin & Promotion Analytics

A 4-page Power BI dashboard analyzing sales, profitability, and promotional performance for a pharmacy distributor operating across 8 European countries.

Built on a star-schema data model (1 fact table, 3 dimension tables) covering two full fiscal years (2024–2025), ~62,000 transactions, and 5 product categories.

---

## 📊 Key Insights

**Portfolio overview**
- Total revenue: **€8.63M**, total margin: **€2.42M** — a portfolio-wide margin of **28.04%**
- **446K units** sold across two years
- Revenue shows a clear upward trend from 2024 into 2025, with a modest seasonal dip visible around early 2025 before recovering

**Category mix is broad, with Prescription leading**
- **Prescription: €2.80M (32.4%)** — the largest single category
- OTC: €1.80M (20.82%), Personal Care: €1.71M (19.83%), Wellness: €1.45M (16.85%), Medical Devices: €0.87M (10.11%)
- No single category dominates the way "Mr. Fuzzy" does in the e-commerce project — this is a genuinely diversified portfolio

**Germany and Italy anchor the network**
- **Germany: €1.57M revenue, 22 active pharmacies** — the single largest market
- **Italy: €1.33M revenue, 18 pharmacies**, close behind
- Margin % is remarkably consistent across countries (27.8%–28.2% band) — no country is dragging down profitability disproportionately, despite revenue varying more than 2x between the smallest (Austria, ~€683K) and largest (Germany) markets
- **120 active pharmacies** total, generating **€71.95K average revenue per pharmacy**

**Urban pharmacies outperform Suburban and Rural**
- Urban pharmacies generate the highest revenue, followed by Suburban, then Rural — a fairly typical retail-density pattern

**Product portfolio is heavily branded, not generic**
- **Branded Revenue: 85.40%** vs. **Generic Revenue: 14.60%**
- **185 active products** generating revenue
- Top 5 products by revenue are dominated by Prescription antibiotics (AntiBioX line) — together contributing roughly **€485K** of the dashboard's visible top-5 total, with margins ranging from **19.36% to 27.15%**
- The scatter plot (price vs. margin vs. units sold) shows most products clustered at lower price points (under €50) with margins spread between roughly 10–30%, with a visible secondary cluster of higher-priced, generally lower-margin products

**Promotions play a modest but measurable role**
- Only **10.55%** of total revenue (**€911.30K** of €8.63M) comes from promoted sales — the large majority of revenue (**€7.72M**) is non-promotional
- **Prescription** generates the most promo revenue in absolute terms (**€312.76K**), consistent with it being the largest category overall — but proportionally, no category appears to lean unusually heavily on promotions
- Revenue YoY % trends mostly positive through 2025, with a brief dip below 0% in early 2025 before climbing back toward double-digit growth by mid-year

---


## 🗂️ Data Model

Star schema with one fact table and three dimension tables, all single-direction relationships (dimension → fact):

| Table | Description |
|---|---|
| `FactSales` | ~62,000 transaction-level sales records: units, revenue, cost, margin, promo flag |
| `DimDate` | Calendar table (2024–2025), marked as the model's official date table |
| `DimPharmacy` | Pharmacy locations: country, region, city, type, size band, open date, coordinates |
| `DimProduct` | Product catalog: category, brand, generic/branded flag, list price, discontinued status |

**Hierarchies:**
- **Geography:** Country → Region → City → Pharmacy Name
- **Product:** Category → Brand → Product Name
- **Time:** Year → Quarter → Month → Date

---

## 🧮 Key DAX Measures

```dax
Total Revenue = SUM(FactSales[RevenueEUR])
Total Margin = SUM(FactSales[MarginEUR])
Margin % = DIVIDE([Total Margin], [Total Revenue], 0)
Revenue YoY % = DIVIDE([Total Revenue] - [Revenue PY], [Revenue PY], 0)
Promo Revenue % = DIVIDE([Promo Revenue], [Total Revenue], 0)
Generic Revenue % = DIVIDE([Generic Revenue], [Total Revenue], 0)
Active Pharmacy Count = DISTINCTCOUNT(FactSales[PharmacyID])
Revenue per Pharmacy = DIVIDE([Total Revenue], [Active Pharmacy Count], 0)
```

20+ measures in total, covering revenue/margin, time intelligence (YTD/MTD/YoY/MoM), promotions, generics vs. branded, and network performance.

---

## 🛠️ Tools & Skills Used

- **Power BI Desktop** — data modeling, DAX, report design
- **Star schema modeling** — fact/dimension design, single-direction relationships
- **Time intelligence DAX** — `SAMEPERIODLASTYEAR`, `TOTALYTD`, `DATEADD`
- **Drill-down hierarchies** — Geography, Product, Time
- **Custom matrix conditional formatting** — data bars, background color scales

---

## 📁 Repository Contents

| File | Description |
|---|---|
| `Pharmacy_Dashboard.pbix` | The full Power BI report |
| `Pharmacy_Data.xlsx` | Source dataset (star schema: fact + dimension tables) |
| `README.md` | This file |

---

## 🚀 How to Use

1. Clone or download this repository.
2. Open `Pharmacy_Dashboard.pbix` in [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free).
3. All data is embedded in the file — no additional setup required.
4. Use the Year/Quarter slicers on any page to filter the entire report.

---

## 📌 Notes

- Some pharmacies and products have open/discontinued dates — zero-sales periods before opening or after discontinuation are expected, not data errors.
- `MarginEUR` is pre-calculated in the source data as `RevenueEUR − CostEUR`.
- Geographic coordinates are slightly jittered for map readability.

---

*Built as a portfolio project to demonstrate Power BI data modeling, DAX, and dashboard design for a multi-country retail/distribution scenario.*
