# AtliQ Hotels — Hospitality Performance Analytics Dashboard

**End-to-end revenue intelligence across 25 properties, 4 cities, and 135K bookings · Built in Power BI**

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?logo=powerbi&logoColor=black)
![Power Query](https://img.shields.io/badge/Power_Query-Data_Transform-2C3E50?logo=microsoft&logoColor=white)
![DAX](https://img.shields.io/badge/DAX-Measures_%26_Calculations-0078D4?logo=microsoft&logoColor=white)
![Data Modeling](https://img.shields.io/badge/Data_Modeling-Star_Schema-1B4F72?logo=databricks&logoColor=white)
![Deneb](https://img.shields.io/badge/Deneb-Vega--Lite-E05C2A?logoColor=white)
![Domain](https://img.shields.io/badge/Domain-Hospitality_Analytics-C9A84C)
![Pages](https://img.shields.io/badge/Pages-7_Interactive-27AE60)
![Status](https://img.shields.io/badge/Status-Complete-success)

---

## Dashboard Preview

![AtliQ Hospitality Performance Analytics Dashboard](assets/dashboard_preview.png)

---

## The Problem

AtliQ Hotels is a fictional mid-to-luxury Indian hotel group running seven brands across Delhi, Mumbai, Hyderabad, and Bangalore. Despite operating at scale — 25 properties, hundreds of daily bookings, four distinct market segments — the business had no consolidated view of its own performance. Revenue was being tracked. Occupancy was being tracked. But nobody was watching them together, across properties, over time, with enough granularity to catch the patterns that matter.

This dashboard is the answer to that gap. It covers May through July 2022 — 92 days, 14 weeks — and turns 134,590 individual booking records into a picture of where the portfolio stands, what is working, and what is quietly draining revenue every week.

---

## What the Data Revealed

**The Luxury category is not earning a premium.** Business category RevPAR (₹7,508) outperforms Luxury RevPAR (₹7,256) by 3.4%. The ADR gap between the two is ₹19 on a ₹12,000+ base — effectively nothing. Luxury properties also run lower occupancy than Business (57.7% vs. 59.8%). In a well-run portfolio, Luxury should justify its higher operating costs through meaningfully higher rates and at least equivalent demand. Neither condition holds here.

**₹29.87 Crore disappears to cancellations every quarter.** The portfolio generated ₹200.75 Cr across the period, but only ₹170.88 Cr was ever realized. That ₹29.87 Cr gap is entirely attributable to a 24.8% cancellation rate — 5 to 10 percentage points above the 15–20% Indian luxury hotel benchmark. The figure is not an abstraction; it is approximately one full month of portfolio revenue, lost on repeat.

**Weekend demand is 44% higher than weekday demand. The rate is identical.** Weekend occupancy runs at 74.3%; weekday occupancy at 51.8%. Weekend ADR: ₹12,577. Weekday ADR: ₹12,574. The pricing system is not responding to demand signals that are visible in the data every single week. A 15% weekend rate premium applied to existing booking volumes — with no change in marketing or capacity — represents an estimated ₹5.7 Crore in incremental quarterly revenue.

**Guest satisfaction data is structurally incomplete.** The portfolio average rating of 3.62/5 is based on 56,683 responses out of 134,590 bookings — a 42.1% response rate. Guests who did not rate are not randomly distributed; they skew toward neutral experiences. Every rating metric in this dashboard is disclosed with this caveat because the alternative — treating the silent majority as absent from the analysis — quietly inflates the numbers.

---

## Dashboard

The report runs across seven pages. Each one answers a question a revenue manager would actually ask, and each is fully interactive through four slicers: City, Property Name, Room Class, and Week Number.

**Page 1 — Executive Overview** opens with eight KPI cards (Revenue · RevPAR · ADR · Occupancy % · Revenue Realisation % · Average Rating · Cancellation % · Total Bookings) followed by a city revenue donut, weekly occupancy trend line, weekly booking volume column chart, average rating gauge, property revenue bar chart, and a property-level scatter positioning ADR against occupancy. The full portfolio story is readable in under thirty seconds.

**Page 2 — Revenue Analysis** traces revenue from source to leakage. A weekly revenue trend line sits alongside a revenue-by-city breakdown, a revenue-by-room-class comparison, a week-by-week change chart, a monthly revenue summary, and a top-ten properties ranking. The Revenue WoW Change % KPI (−0.82% for the full period) surfaces the flat-to-declining trajectory that monthly totals obscure.

**Page 3 — Occupancy & Capacity** covers the capacity utilization chain — Occupancy %, DSRN, DBRN, DURN, and Revenue Realisation % — broken down by city, room class, and week. The DSRN vs. DBRN vs. DURN trend chart makes the gap between available, booked, and utilized room-nights visible as a time series.

**Page 4 — Booking Behavior** examines how bookings arrive and how they resolve. Platform-level volumes, booking status distribution (Checked Out 70.1% / Cancelled 24.8% / No Show 5.0%), bookings by room class, and top-ten properties by total bookings. The platform analysis confirms no single channel is meaningfully better or worse on realisation — the cancellation problem is not a distribution problem.

**Page 5 — Property Scorecard** is the benchmarking page. A pivot matrix shows all tracked properties against Revenue, Occupancy %, ADR, RevPAR, Average Rating, Cancellation %, and Revenue Realisation % simultaneously. An average rating by property chart shows the ±2 point spread between the portfolio's best and worst guest experience performers.

**Page 6 — Weekly Trends** runs the key metrics week by week across all 14 weeks: Revenue, Occupancy %, ADR, RevPAR, Realisation %, and Average Rating. The bi-weekly demand cycle — high revenue weeks alternating with sharp troughs — is most visible here. The −63% single-week drop at W23 and the +166% rebound at W21 are not anomalies; they are a repeating pattern that monthly reporting completely obscures.

**Page 7 — About / Info Dashboard** documents the project: KPI definitions, data sources, business questions the report is designed to answer, and the full technical stack. It is the first page anyone new to the file should read.

---

## Data Model

```
                          dim_date  ◄────────────────────────┐
                         (PK: date)                          │
                         /        \                          │
               [ACTIVE] /          \ [ACTIVE]               │
                       ↓            ↓                       │
              fact_bookings   fact_aggregated_bookings       │
              (134,590 rows)  (property × date × room)       │
                       \            /                       │
                        ↓          ↓                        │
                     dim_hotels   dim_rooms     key_measures │
                     (25 rows)    (4 rows)      (no data)    │
```

`fact_bookings` is the individual booking grain — one row per booking, carrying revenue, status, platform, guest count, and rating. `fact_aggregated_bookings` is the capacity grain — one row per property × date × room type, carrying `capacity` and `successful_bookings`. These two tables cannot be merged. RevPAR requires capacity as its denominator, and capacity only exists in `fact_agg`. Any RevPAR derived solely from `fact_bookings` would be a proxy calculation with compounding error.

`dim_hotels` maps property IDs to names, cities, and the Luxury/Business category. `dim_rooms` maps RT1–RT4 codes to Standard, Elite, Premium, and Presidential labels. The `key_measures` table holds no data rows; it exists solely as an organizational container for all DAX measures. Three relationships connect `fact_bookings` to the Calendar: one active on `check_in_date`, two inactive on `booking_date` and `checkout_date`, activated in specific measures via `USERELATIONSHIP()`.

---

## Measures

| Measure | Definition |
|---------|-----------|
| Revenue | `SUM(fact_bookings[revenue_realized])` — amount received after applying the cancellation retention rule |
| Revenue Realisation % | Revenue ÷ `SUM(fact_bookings[revenue_generated])` |
| Revenue WoW Change % | Week-over-week revenue delta using `DATEADD(Calendar[Date], -7, DAY)` |
| RevPAR | Revenue ÷ `SUM(fact_aggregated_bookings[capacity])` |
| ADR | Revenue ÷ `SUM(fact_aggregated_bookings[successful_bookings])` |
| Occupancy % | `SUM(successful_bookings)` ÷ `SUM(capacity)` — both from `fact_aggregated_bookings` |
| DSRN | `SUM(capacity)` ÷ `COUNTROWS(Calendar)` |
| DBRN | `COUNTROWS(fact_bookings)` ÷ `COUNTROWS(Calendar)` |
| DURN | Checked Out count ÷ `COUNTROWS(Calendar)` |
| Total Bookings | `COUNTROWS(fact_bookings)` |
| Cancellation % | Cancelled Bookings ÷ Total Bookings |
| Average Rating | `AVERAGEX` over rows where `ratings_given` is not blank |
| Rating Target | Static constant used as the gauge visual's goal marker |

`DIVIDE()` replaces `/` throughout — the difference surfaces when a denominator reaches zero, which happens whenever a slicer isolates a property with no bookings in a given week. Average Rating uses `AVERAGEX` with an explicit null filter rather than `AVERAGE()` to make the intent auditable and prevent silent behavior changes if the null pattern in source data shifts.

---

## Technical Stack

| Component | Badge | Detail |
|-----------|-------|--------|
| BI Tool | ![Power BI](https://img.shields.io/badge/Power_BI-F2C811?logo=powerbi&logoColor=black) | Desktop 2026.07 · Import mode · 9.08 MB |
| Transformations | ![Power Query](https://img.shields.io/badge/Power_Query-2C3E50?logo=microsoft&logoColor=white) | M language · typo fix · date parsing |
| Calculations | ![DAX](https://img.shields.io/badge/DAX-0078D4?logo=microsoft&logoColor=white) | 13 measures · `key_measures` table |
| Schema | ![Data Modeling](https://img.shields.io/badge/Star_Schema-1B4F72?logo=databricks&logoColor=white) | 2 fact tables · 3 dimensions · full Calendar |
| Custom Visual 1 | ![Deneb](https://img.shields.io/badge/Deneb-Vega--Lite-E05C2A) | Pixel-level layout and annotation control |
| Custom Visual 2 | Charticulator | Custom chart geometry beyond native types |
| Custom Visual 3 | Advanced Pie & Donut | Enhanced donut chart rendering |

Import mode is appropriate at this scale — 134,590 rows compress under 10 MB and return sub-second query responses on every slicer interaction with no real-time refresh requirement. Deneb is used where native Power BI chart types would require compromising layout or annotation density.

---

## Dataset

| File | Rows | Description |
|------|------|-------------|
| `fact_bookings.csv` | 134,590 | One row per booking: revenue, status, platform, rating, guest count |
| `fact_aggregated_bookings.csv` | 9,200 | Daily capacity and successful bookings by property and room type |
| `dim_date.csv` | 92 | Calendar for May–July 2022; replaced in model by full-year DAX table |
| `dim_hotels.csv` | 25 | Property master: name, city, Luxury/Business category |
| `dim_rooms.csv` | 4 | Room code to class label mapping (RT1–RT4 → Standard through Presidential) |
| `metrics_list.xlsx` | 8 | Official KPI definitions from the business stakeholder |

**Data quality fix.** The `day_type` column in `dim_date` contains `"weekeday"` — a typo — in 65 of 92 rows. Any DAX measure filtering `dim_date[day_type] = "Weekday"` returns blank for those dates if loaded uncorrected. Fixed via a single `Table.ReplaceValue` step in Power Query before the table loads into the model.

**Null handling.** `fact_bookings[ratings_given]` is null for 57,907 of 134,590 records (43%). Every rating visual includes a 42.1% response coverage disclosure.

---

## Benchmarks

| KPI | Portfolio Actual | Indian Luxury Benchmark | Status |
|-----|-----------------|------------------------|--------|
| Occupancy % | 57.87% | 65–72% | ↓ Below |
| Cancellation Rate | 24.8% | 15–20% | ↑ Elevated |
| Average Rating | 3.62 / 5 | 4.0–4.3 / 5 | ↓ Below |
| Revenue Realisation % | 85.1% | 88–93% | ↓ Below |
| RevPAR | ₹7,347 | ₹7,500–12,000 | → At floor |
| ADR | ₹12,696 | ₹12,000–18,000 | ✓ Within range |

ADR is the one metric where the portfolio is competitive. Everything else — occupancy, cancellation rate, realisation, guest satisfaction — sits below benchmark. The lever is demand retention, not rate increases.

---

## Getting Started

1. Open `AtliqHospitality_Analysis_Dashboard.pbix` in Power BI Desktop (July 2024 release or later).
2. No database connection or gateway is required. All data is embedded via Import mode.
3. To reconnect to raw CSV files, go to **Home → Transform Data → Data Source Settings** and update the file paths.
4. Custom visuals (Deneb, Charticulator, Advanced Pie & Donut) are bundled inside the `.pbix` — no separate installation needed.
5. Start on **Page 7 (About / Info)** if you are new to the file. Start on **Page 1 (Executive Overview)** if you are a stakeholder.

---

## Project Structure

```
AtliQ-Hospitality-Dashboard/
│
├── AtliqHospitality_Analysis_Dashboard.pbix    # Main Power BI report (9.08 MB)
│
├── assets/
│   └── dashboard_preview.png                   # Screenshot used in this README
│
├── data/
│   ├── fact_bookings.csv                        # 134,590 rows
│   ├── fact_aggregated_bookings.csv             # 9,200 rows
│   ├── dim_date.csv                             # 92 rows
│   ├── dim_hotels.csv                           # 25 rows
│   ├── dim_rooms.csv                            # 4 rows
│   └── metrics_list.xlsx                        # 8 KPI definitions
│
└── README.md
```

---

## About the Developer

Built by **Khusi Khanra** · [github.com/khusikhanra](https://github.com/khusikhanra)

---

*Power BI · DAX · Power Query · Data Modeling · Hospitality Analytics · Revenue Management · Hotel KPIs · RevPAR · ADR · Occupancy Analysis*
