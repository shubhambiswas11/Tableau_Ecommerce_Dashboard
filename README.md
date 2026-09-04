# E-commerce Sales Dashboard | Tableau

An interactive Tableau dashboard analyzing e-commerce sales performance (YTD 2017) across regions, categories, and time — built to surface sales/profit/order trends and customer acquisition patterns.

## 🔗 Preview
![Dashboard Screenshot](dashboard_screenshot.png)

## 📊 What It Shows

**KPI Cards (Sales, Profit, Order Quantity)**
Each card shows: current YTD value, YoY % change with ▲/▼ arrow (color-coded), and an inline sparkline trend — all built in a **single sheet** (not split across sheets) to keep dashboard performance efficient.

**Market Share by Sales**
Custom donut chart (dual-axis pie technique) showing % contribution to YTD sales by region — Europe, LATAM, Pacific Asia, Africa, USCA.

**Category-wise YTD vs PYTD Sales**
Top 10 categories ranked by YTD sales, with a bar-in-bar KPI showing % change vs last year. Color-coded: green (growth), red (decline), gray (no sales in previous year — new category).

**Market-wise Customer Acquisition per Month**
Bump chart (rank-based line + circle) showing each region's monthly rank in order volume across the year — highlights festive-season spikes (Nov–Jan).

**Filters**
Market and Customer Segment — both set as **Context Filters** (required because FIXED LOD calculations only execute after context filters are applied).

## 🧮 Key Calculated Fields

**YTD Sales (LOD)** — used for KPI cards
```
{FIXED : SUM(
  IF YEAR([Order Date]) = YEAR({FIXED : MAX([Order Date])})
  THEN [Sales] END
)}
```
Same logic duplicated for Profit and Order Quantity by swapping the measure.

**Previous YTD Sales (LOD)** — same formula, but compares against `YEAR(...) - 1`

**YoY Sales %**
```
(SUM([YTD Sales]) - SUM([Previous YTD Sales])) / SUM([Previous YTD Sales])
```

**YoY Sales Margin (arrow indicator)**
```
IF [YoY Sales %] > 0 THEN '▲'
ELSEIF [YoY Sales %] < 0 THEN '▼'
END
```

**YTD Sales — non-LOD version** (for the donut chart)
```
IF YEAR([Order Date]) = {FIXED : MAX(YEAR([Order Date]))}
THEN [Sales] END
```
Note: the *FIXED LOD* version can't be used here — FIXED is independent of the view, so it ignores the Market dimension on the chart and returns the same total for every slice. This simple aggregate version respects the view context instead.

**Percentage Difference (category chart)**
```
(SUM([YTD Sales]) - SUM([Previous YTD Sales])) / SUM([Previous YTD Sales])
```

**Sales Margin (category color logic)**
```
IF SUM([YTD Sales]) - SUM([Previous YTD Sales]) < 0 THEN "Less than previous year"
ELSEIF SUM([YTD Sales]) - SUM([Previous YTD Sales]) > 0 THEN "Greater than previous year"
ELSEIF ISNULL(SUM([Previous YTD Sales])) THEN "No sales in previous year"
END
```

**Rank (bump chart)** — Quick Table Calculation (Rank), computed using Market, to plot each region's monthly order-volume ranking.

## 🛠️ Tools & Techniques
- **Tool:** Tableau Desktop
- **Calculations:** FIXED LOD expressions, YoY % change, dual-axis charts (donut, bump chart), quick table calculations (Rank, % of Total)
- **Dashboard design:** Horizontal/vertical containers, floating legends, context filters, action filters (donut chart click filters other views)
- **Data:** CSV, ~115,000 rows, e-commerce order-level data (2016–2017)

## 📁 Files
- `dashboard.twbx` — Packaged Tableau workbook (includes data)
- `dashboard_screenshot.png` — Static preview image

## 🎯 Key Insight
Despite 5% YoY growth in overall sales, profit declined slightly (-0.46%) — a good talking point on margin pressure despite volume growth. Regionally, LATAM and Europe drive ~89% of total sales, while customer acquisition peaks in Nov–Jan across most regions (festive season effect).

---
*Built as a self-driven analytics project to practice LOD calculations, dashboard design, and business storytelling in Tableau.*
