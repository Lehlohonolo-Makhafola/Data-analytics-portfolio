# 🚗 Dealership Sales — Data Cleaning & Dashboard Project

> **Tools:** Microsoft Excel · Pivot Tables · Dashboard Design  
> **Dataset:** 2,823 rows × 25 columns · Orders spanning 2003–2005  
> **Deliverables:** Cleaned dataset · Issues log · Pivot tables · Interactive dashboard

---

## 📋 Project Overview

This project demonstrates end-to-end data cleaning and business intelligence reporting on a multi-year dealership sales dataset. The raw dataset contained structural inconsistencies, incorrect revenue calculations affecting **1,304 rows** (46% of all records), date formatting issues, misspelled customer names, and non-standard column naming conventions.

The cleaned dataset was used to build an interactive Excel dashboard surfacing key business metrics: total revenue, top product lines, market performance by region, deal size distribution, monthly sales trends, and customer rankings.

---

## 📁 Repository Structure

```
dealership-sales/
│
├── Dealership_sales__Raw_.csv          # Source data (unmodified)
├── Dealership_sales_project.xlsx       # Cleaned workbook with 4 sheets:
│   ├── Dealership sales                # Cleaned dataset
│   ├── Issues log                      # Data quality audit log
│   ├── Pivot tables                    # Analysis pivot tables
│   └── Dashboard                       # Interactive Excel dashboard
│
├── Dealership_sales_dashboard.png      # Dashboard screenshot
└── README.md
```

---

## 🔍 Data Profiling — Raw Dataset

| Attribute | Detail |
|---|---|
| Source format | CSV (Latin-1 encoded) |
| Rows | 2,823 |
| Columns | 25 |
| Years covered | 2003, 2004, 2005 |
| Countries | 19 |
| Product lines | 7 |
| Unique customers | 92 |
| Duplicate rows | 0 |

**Columns in raw file:**

`ORDERNUMBER` · `QUANTITYORDERED` · `PRICEEACH` · `ORDERLINENUMBER` · `SALES` · `ORDERDATE` · `STATUS` · `QTR_ID` · `MONTH_ID` · `YEAR_ID` · `PRODUCTLINE` · `MSRP` · `PRODUCTCODE` · `CUSTOMERNAME` · `PHONE` · `ADDRESSLINE1` · `ADDRESSLINE2` · `CITY` · `STATE` · `POSTALCODE` · `COUNTRY` · `TERRITORY` · `CONTACTLASTNAME` · `CONTACTFIRSTNAME` · `DEALSIZE`

---

## 🛠️ Data Issues Identified & Resolved

### Issue 1 — Incorrect Revenue Values (HIGH IMPACT)
**Column:** `SALES` → renamed `Revenue`

The `SALES` column contained incorrect values in **1,304 out of 2,823 rows (46.2%)** — a critical data integrity failure. The correct revenue for each line item is `QUANTITY_ORDERED × PRICE_EACH`. The original values did not match this formula and would have produced a materially incorrect total revenue figure had they been used for reporting.

| Metric | Value |
|---|---|
| Rows affected | 1,304 (46.2%) |
| Resolution | Recalculated as `QUANTITY_ORDERED × PRICE_EACH` |
| Impact | High — all revenue aggregations depended on this |

**Example discrepancy:**

| ORDER_NUMBER | QTY | PRICE | Original SALES | Corrected Revenue |
|---|---|---|---|---|
| 10159 | 49 | 100.00 | 5,205.27 | 4,900.00 |
| 10188 | 48 | 100.00 | 5,512.32 | 4,800.00 |
| 10211 | 41 | 100.00 | 4,708.44 | 4,100.00 |

---

### Issue 2 — Date Field Stored as Text with Trailing Time Component (HIGH IMPACT)
**Column:** `ORDERDATE` → renamed `ORDER_DATE`

All 2,823 date values were imported as text strings in the format `M/DD/YYYY 0:00`, with a trailing midnight time component appended. This prevented Excel from recognising the field as a proper date, breaking any date-based calculations, time intelligence functions, or pivot table grouping by month/year.

**Resolution:** Used the `TEXTBEFORE()` formula to extract the date component before the space character, then formatted the column as a date type.

```
=TEXTBEFORE([@ORDERDATE], " ")
```

| Before | After |
|---|---|
| `2/24/2003 0:00` | `2/24/2003` |
| `5/7/2003 0:00` | `5/7/2003` |
| `11/11/2004 0:00` | `11/11/2004` |

---

### Issue 3 — Misspelled Customer Names (LOW IMPACT)
**Column:** `CUSTOMERNAME` → renamed `CUSTOMER_NAME`

Multiple customer names contained the misspelling **"Souveniers"** (incorrect) instead of **"Souvenirs"** (correct). This would cause customer-level grouping in pivot tables and reports to silently split records for the same customer across two differently-spelled names, corrupting customer revenue totals.

A second distinct misspelling — **"Spezialitten"** instead of **"Spezialitäten"** — was also corrected, restoring the proper German umlaut character.

**Customers corrected:**

| Raw Name | Corrected Name |
|---|---|
| Lyon Souveniers | Lyon Souvenirs |
| Dragon Souveniers, Ltd. | Dragon Souvenirs, Ltd. |
| Osaka Souveniers Co. | Osaka Souvenirs Co. |
| Souveniers And Things Co. | Souvenirs And Things Co. |
| L'ordine Souveniers | L'ordine Souvenirs |
| Suominen Souveniers | Suominen Souvenirs |
| Toms Spezialitten, Ltd | Toms Spezialitäten, Ltd |

Title Case was also applied consistently across all customer names.

---

### Issue 4 — Phone Numbers: Non-Standardisable Across Jurisdictions (NOTED — NOT RESOLVED)
**Column:** `PHONE`

The dataset spans 19 countries. Phone numbers appear in inconsistent formats — some with country codes, some without, and local formatting conventions varying by country. Standardising these into a unified international format (e.g., E.164) without a reference table mapping each customer's country code would introduce errors.

**Decision:** The `PHONE` column was left as-is. Flagged in the issues log for stakeholder consultation before any standardisation is applied.

---

### Issue 5 — Blank Cells in `ADDRESS_LINE_2` and `STATE` (LOW IMPACT)
**Columns:** `ADDRESSLINE2` → `ADDRESS_LINE_2` | `STATE`

Both columns contained a large number of blanks that are structurally expected (not all addresses have a second line; many countries do not use a state/province field). However, blank cells reduce readability and can cause issues in certain formula contexts.

**Resolution:** `IF()` formula applied to replace blank cells with the string `"N/A"` to make nulls explicit and readable.

| Column | Blank count | Treatment |
|---|---|---|
| ADDRESS_LINE_2 | 2,521 of 2,823 | Replaced with "N/A" |
| STATE | 1,486 of 2,823 | Replaced with "N/A" |

---

## 🔄 Structural Changes Applied

### Column Renaming — Standardised to UPPER_SNAKE_CASE

All 25 column names were renamed from inconsistent concatenated ALLCAPS to consistent `UPPER_SNAKE_CASE`, improving readability and compatibility with SQL and BI tools.

| Raw Name | Cleaned Name |
|---|---|
| ORDERNUMBER | ORDER_NUMBER |
| QUANTITYORDERED | QUANTITY_ORDERED |
| PRICEEACH | PRICE_EACH |
| ORDERLINENUMBER | ORDERLINE_NUMBER |
| SALES | Revenue |
| ORDERDATE | ORDER_DATE |
| PRODUCTLINE | PRODUCT_LINE |
| PRODUCTCODE | PRODUCT_CODE |
| CUSTOMERNAME | CUSTOMER_NAME |
| ADDRESSLINE1 | ADDRESS_LINE_1 |
| ADDRESSLINE2 | ADDRESS_LINE_2 |
| POSTALCODE | POSTAL_CODE |
| CONTACTLASTNAME | CONTACT_LAST_NAME |
| CONTACTFIRSTNAME | CONTACT_FIRST_NAME |

---

## 📊 Pivot Table Analysis

Five pivot tables were built on the cleaned dataset to support dashboard construction:

| Pivot Table | Insight |
|---|---|
| Revenue by Country | USA leads at 36.02% of total revenue |
| Revenue by Product Line | Classic Cars top performer at $2,968,546 (35.8%) |
| Revenue by Territory | EMEA dominates with 49.79% of total revenue |
| Revenue by Month × Year | November peaks every year — signals seasonal demand |
| Revenue by Deal Size | Medium deals generate 59.85% of revenue |
| Revenue by Top 10 Customers | Euro Shopping Channel is the #1 customer at $766,195 |

---

## 📈 Dashboard

The interactive dashboard was built in Excel with slicers for **Status**, **Country**, **Deal Size**, and **Territory**, allowing dynamic filtering across all visuals.

![Dealership Sales Dashboard](Dealership_sales_dashboard.png)

### KPI Cards

| KPI | Value |
|---|---|
| Total Revenue | $8,290,886.79 |
| Average Order Value | $27,006.15 |
| Total Units Sold | 91,403 |
| Top Product Line | Classic Cars — $2,968,546.40 |
| Top Market | USA — $2,986,425.21 |
| Best Customer | Euro Shopping Channel — $766,195.05 |
| Revenue at Risk | $639,747.43 (Cancelled/Disputed orders) |

### Visuals Included

- **Horizontal bar chart** — Revenue % by country
- **Donut chart** — Revenue share by territory (EMEA, NA, APAC, Japan)
- **Bar chart** — Revenue by product line
- **Multi-line chart** — Monthly revenue trend by year (2003–2005)
- **Donut chart** — Revenue distribution by deal size
- **Multi-line chart** — Top 10 customers by revenue across years

---

## 🗂️ Issues Log

A structured issues log was maintained throughout the cleaning process, documenting every finding regardless of whether it was resolved.

| Table | Column | Issue | Solvable | Resolution | Impact |
|---|---|---|---|---|---|
| Dealership sales | ORDER_DATE | Dates stored as text with trailing `0:00` time string | YES | `TEXTBEFORE()` to extract date before the space | High |
| Dealership sales | Revenue | Incorrect/inconsistent sales values in 1,304 rows | YES | Recalculated as `QUANTITY_ORDERED × PRICE_EACH` | High |
| Dealership sales | CUSTOMER_NAME | Misspelling: "Souverniers" → "Souvenirs" | YES | Find & replace across 6 customer names | Low |
| Dealership sales | CUSTOMER_NAME | Misspelling: "Spezialitten" → "Spezialitäten" | YES | Corrected with proper umlaut character | Low |
| Dealership sales | PHONE | Multi-country dataset — no reliable standardisation without country code reference | NO | Flagged for stakeholder decision; column kept as-is | Low |
| Dealership sales | ADDRESS_LINE_2 | 2,521 blank cells | YES | `IF()` formula — blanks replaced with "N/A" | Low |
| Dealership sales | STATE | 1,486 blank cells | YES | `IF()` formula — blanks replaced with "N/A" | Low |

---

## 💡 Key Insights from the Analysis

- **November is the standout month** — revenue peaks every year in Month 11 across 2003 and 2004, signalling a consistent seasonal demand cycle worth planning inventory and promotions around.
- **Classic Cars dominate** — accounting for 35.8% of total revenue, nearly 2× the next product line (Vintage Cars at 19.8%).
- **EMEA is the leading territory** — contributing nearly 50% of global revenue, with Spain and France as the strongest European markets after the USA.
- **Medium deals drive the business** — 59.85% of revenue comes from medium-sized deals, suggesting the core customer segment is mid-market buyers rather than large enterprise or retail.
- **Revenue at risk is non-trivial** — $639,747 tied to cancelled or disputed orders warrants an operational review of fulfilment and dispute resolution processes.
- **Euro Shopping Channel stands out** — the top customer by a significant margin, generating $766,195 over the period.

---

## ✅ Skills Demonstrated

- Data profiling and quality auditing
- Formula-based cleaning (`TEXTBEFORE`, `IF`, arithmetic recalculation)
- Column standardisation and naming conventions
- Pivot table design and multi-dimensional aggregation
- Interactive dashboard design with slicers and KPI cards
- Issues log documentation and stakeholder communication
- Business insight extraction from cleaned data

---

*Dataset source: Public sample sales dataset. All analysis performed in Microsoft Excel.*
