\# Credit Card Fraud Detection — Excel Risk Scoring Engine \& Dashboard



\*\*Built by Hloni Makhafola\*\* · Excel · PivotTables · Formula Engineering · Dashboard Design



> A 738-transaction fraud monitoring system built from a messy raw dataset, a custom six-factor risk-scoring model, and an interactive dashboard — with findings that were tested against my own assumptions, not just built to confirm them.



\---



\## At a Glance



| Total Transactions | Total Value at Risk (ZAR) | Cardholders | High-Risk Rate | Declined Rate |

|---|---|---|---|---|

| \*\*738\*\* | \*\*R1,423,975.85\*\* | \*\*62\*\* | \*\*3.12%\*\* | \*\*20.60%\*\* |



\---



\## Why This Project



Most beginner Excel portfolios stop at "clean the data, build a pivot, add a chart." This one goes a step further: I built a genuine risk-scoring model, then actively tried to break my own assumptions about what the data would show. A few findings below directly contradict what I expected going in — and I've kept them in, because catching that is a more useful skill than a dashboard where every chart conveniently agrees with the story I wanted to tell.



My accounting and finance background — reconciliations, AP/AR, cost accounting — directly shaped the fraud logic here: the risk checks reflect real transaction-risk patterns I've encountered, not textbook assumptions. That context shaped the fraud logic here: the risk checks aren't arbitrary, they reflect real transaction-risk patterns (card-not-present exposure, velocity fraud, category-specific vulnerability) that show up in real payment operations.



\---



\## The Data



\### Raw → Cleaned



The raw export had genuine, realistic data-quality problems — not planted for show, but the kind you'd actually hit pulling transaction data out of a legacy system. Every issue is logged in the workbook's `Issues\_Log` tab with row counts and resolutions, so the cleaning process is fully auditable.



| Issue | Rows Affected | Resolution |

|---|---|---|

| Mixed date formats (text strings + Excel datetime objects) | 362 | Rebuilt dates with a locale-independent `DATE()` formula rather than trusting Excel's auto-parse, which was silently misreading `dd/mm` as `mm/dd` |

| Missing timestamps | 43 | Filled with an explicit `"Unknown"` placeholder instead of deleting rows or guessing a time — preserves row count and flags the gap for downstream logic |

| Abbreviated city names (PE, PTA, JHB, CPT, Bloem) | 278 (+23 in reference table) | Standardized to full, proper-case city names for consistent grouping |

| Currency stored as text (`"R"` / `"ZAR"` symbols, commas) | 90 (+5 in reference table) | Stripped symbols and separators via nested `SUBSTITUTE`, converted to true numeric values |



Raw and cleaned versions were kept as separate sheets throughout — nothing was overwritten silently.



\### The Risk Engine



Every transaction runs through \*\*six independent binary checks\*\*, summed into a `Risk\_Score` (0–6) and bucketed into `Risk\_Level` (Low / Medium / High):



\- \*\*City\_Check\*\* — flags transactions inconsistent with the cardholder's registered location

\- \*\*Hour\_Check\*\* — flags high-risk time-of-day activity

\- \*\*Velocity\_Check\*\* — flags unusually rapid repeat transactions on the same card

\- \*\*Category\_Check\*\* — flags merchant categories with elevated real-world fraud exposure

\- \*\*Channel\_Check\*\* — flags card-not-present channels (phone, online) over physical card presence

\- \*\*Limit\_Check\*\* — flags transactions pushing close to the cardholder's credit limit



This structure matters: a transaction can be small in value but still genuinely risky if it trips three or four checks at once (classic card-testing behavior) — a value-only view would miss that entirely.



\---



\## Dashboard \& Key Findings



The dashboard is fully interactive — \*\*Category, City, Risk\_Level, Channel, and Card Network slicers\*\* let a viewer drill into any slice of the data. Every chart title states the \*finding\*, not just the axes.



\### 1. Card-Not-Present Channels Drive the Highest Risk



| Channel | Avg. Risk\_Score |

|---|---|

| Online | 1.46 |

| Card Not Present (Phone) | 1.45 |

| Contactless | 0.47 |

| Card Present | 0.45 |



Online and phone-based transactions carry roughly \*\*3x the average risk score\*\* of physical card transactions — exactly what real-world fraud patterns would predict, since neither channel requires physical card verification.



\### 2. Approved vs. Declined: A Finding That Challenged My Assumption



I expected Declined transactions to skew High risk. They don't:



| Status | Low | Medium | High |

|---|---|---|---|

| Approved | 77.1% | 19.5% | 3.4% |

| Declined | 77.6% | 20.4% | 2.0% |



Both groups are dominated by \*\*Low risk\*\*, and Declined is \*not\* meaningfully riskier than Approved. This tells me `Risk\_Score` and the Approved/Declined decision are answering two different questions — in this dataset, declines aren't primarily driven by the fraud model, which mirrors real payment systems where approval logic (funds, limits, card status) and fraud-risk scoring often run as separate systems entirely.



\### 3. Restaurant Leads in Total Risk, But Not Per-Transaction Risk



Restaurant has the highest transaction volume (114 of 738) and therefore the highest \*\*cumulative\*\* risk score — but its \*\*average\*\* risk score per transaction is mid-table:



| Category | Avg. Risk\_Score | Transactions |

|---|---|---|

| Electronics | 1.15 | 47 |

| Utilities | 1.09 | 45 |

| Clothing | 1.06 | 51 |

| Restaurant | 0.97 | 114 |

| ATM Withdrawal | 0.80 | 54 |



Electronics and Utilities carry \*\*higher average risk per transaction\*\* despite far lower volume. Highest Sum ≠ highest Average — conflating the two would misdirect monitoring resources toward the wrong category.



\### 4. Risk Doesn't Scale With Volume: Bank Comparison



| Bank | Avg. Risk\_Score |

|---|---|

| Nedbank | 1.11 |

| Capitec | 0.96 |

| Standard Bank | 0.95 |

| ABSA | 0.92 |

| FNB | 0.88 |



Nedbank shows the highest average risk despite not having the highest transaction count — ranking by average, not raw volume, avoids the same distortion identified in the category breakdown above.



\### 5. Risk Levels Fluctuate Year to Year — No Clear Trend (2025–2027)



| Year | Low | Medium | High |

|---|---|---|---|

| 2025 | 76.2% | 20.7% | 3.1% |

| 2026 | 80.6% | 16.0% | 3.4% |

| 2027 | 72.5% | 25.0% | 2.5% |



\*(2028 excluded — only 10 transactions recorded, an incomplete year that would distort the comparison.)\*



High-risk share moves from 3.1% → 3.4% → 2.5% — fluctuation, not a consistent upward or downward trend. I'm reporting this honestly rather than forcing a tidier narrative: three complete years is a limited sample, and "stable with fluctuation" is the accurate read, not "improving" or "worsening."



\### 6. Makro Leads Top-10 Merchants by Value at Risk



| Merchant | Value at Risk (ZAR) |

|---|---|

| Makro | R165,442.85 |

| Incredible Connection | R161,299.10 |

| Game | R106,375.51 |

| Superbalist | R71,098.03 |

| Takealot | R63,237.41 |



Useful for prioritizing which merchant relationships might warrant tighter monitoring or additional verification steps.



\---



\## Skills Demonstrated



\- \*\*Data Cleaning\*\* — locale-independent date rebuilding, text-to-number currency conversion, standardizing inconsistent categorical entries, all logged with row counts and resolutions

\- \*\*Formula Engineering\*\* — nested IF/SUBSTITUTE logic, XLOOKUP/INDEX-MATCH across sheets, binary risk-check construction

\- \*\*PivotTables \& Data Modeling\*\* — multi-dimensional pivots (Category × Risk\_Level, Status × Risk\_Level), and choosing Sum vs. Average vs. Count deliberately based on the question being asked

\- \*\*Dashboard Design\*\* — insight-driven chart titles, interactive slicers, consistent visual language, KPI cards for at-a-glance exposure reporting

\- \*\*Analytical Judgment\*\* — testing assumptions against the data instead of assuming them, separating volume effects from severity effects, reporting an inconclusive trend honestly instead of overstating it

\- \*\*Domain Context\*\* — real AP/AR and reconciliation experience informing the fraud-typology reasoning behind each risk check



\---



\## What's Next



This project is step one of a structured \*\*Excel → SQL → Power BI → Python\*\* learning path. The next milestone rebuilds this same fraud dataset in SQL — XLOOKUP logic becomes JOINs, the six binary risk checks become `CASE WHEN` statements, and the analysis extends into window functions and CTEs — using this exact dataset as continuity between tools rather than starting from scratch.



\---



\## Repository Contents



\- `Credit\_Card\_Fraud\_RAW.xlsx` — original, unmodified dataset

\- `Credit\_Card\_Fraud\_Cleaned.xlsx` — cleaned data, PivotTables, dashboard, issues log

\- Full formula documentation and issues log inside the workbook (`Issues log` tab)



\---



\*Hloni Makhafola — Finance Administrator Intern, Wits Health Consortium · National Diploma, Cost \& Management Accounting, Vaal University of Technology\*

*Dataset source: Public sample sales dataset. All analysis performed in Microsoft Excel.*

