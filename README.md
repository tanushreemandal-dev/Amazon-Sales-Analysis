# Amazon India Sales Analysis (MS-Excel) 📊

## Overview
Cleaned and analysed ~129k Amazon India sales records (Mar to Jun 2022) and built an Excel dashboard using pivot tables, charts, slicers and a timeline.


## Dataset 
Amazon Sale Report, Kaggle: [https://www.kaggle.com/datasets/arpit2712/amazonsalesreport]
Rows: 128,976 | Columns: 21

## Tools
Excel (pivot tables, pivot charts, slicers, timeline)

## Data Cleaning Steps

### 1. Duplicates 
- Ran Remove Duplicates across all columns and 168 exact duplicate rows were removed.

### 2. Date column
- **Problem:** Dates were stored as text in MM-DD-YY format (e.g. `04-30-22`). When the CSV was opened directly in Excel, about 55,000 cells were auto-converted into real dates and the rest stayed as text. Because the system is set to a DD-MM locale, any date with both parts of 12 or below could silently have its day and month swapped.
- **Fix:** Re-imported the original CSV so the column stayed as text, then used **Data → Text to Columns → Date (MDY)** to convert every cell into a real date. Applied the display format `dd-mm-yyyy`.
- **Validation:** All 128,976 data rows are real dates (status bar numeric count matches the row count). The date filter shows only March to June 2022, with the earliest date 31-03-2022 and the latest about 29-06-2022.

### 3. Removed unnecessary columns
- Deleted `index` (a row counter) and the empty columns `New` and `PendingS`.

### 4. State names (`ship-state`)
- Standardised state names (e.g. Pondicherry → Puducherry, AR → Arunachal Pradesh) 
- Changes were made with Find & Replace using "Match entire cell contents", so partial matches were not affected.

### 5. Missing shipping location
- 33 rows had a blank `ship-state`, `ship-postal-code` and `ship-country`, and a corrupted or blank `ship-city`. The state could not be recovered because the pincode was also missing.
- **Action:** Labelled these `Unknown` rather than deleting them, so their sales stay in revenue totals. They are excluded from state-wise analysis.

### 6. City names (`ship-city`)
- City is free text typed by customers, with typos, extra punctuation and address text mixed in (e.g. `,HYDERABAD`, `..katra`, `Chikmagalur disterict`).
- **Action:** Removed leading and trailing punctuation, trimmed extra spaces and standardised case to upper. Fixed duplicated words such as `NEW NEW DELHI` → `NEW DELHI`. A non-place value (`1`) was labelled `Unknown`.
- **Decision:** City names were not fully standardised, because the typos are mostly in rare cities. Geographic analysis uses `ship-state`.

### 7. Currency
- Blank `currency` cells were on cancelled or in-transit orders with Qty 0 and no Amount. All populated values were INR and the sales channel is Amazon.in, so blanks were filled with `INR` for consistency.

### 8. Amount column
- Rounded `Amount` to whole rupees (stored values, not only display). 
- Blank Amounts belong to cancelled or unfulfilled orders with Qty 0. No values were estimated or filled in.
- **Zero-amount shipped orders:** rows have Status Shipped, Qty above 0 and Amount = 0. The cause is not stated in the source. They were **kept** (they are real shipped items), counted in order and unit totals, and have no effect on revenue totals.

### 9. Status and Courier Status
- "Returned to Seller" and "Returning to Seller" were **kept separate**, because one is a completed return and the other is in progress.
- Used `Status` (not `Courier Status`) for cancellation and return analysis, because `Status` holds the order outcome.

### 10. Duplicates 
- Ran Remove Duplicates across all columns, not just `Order ID`, because one order can have several item rows. [168] exact duplicate rows were removed.

### 11. Other columns
- Columns not used in the analysis (e.g. `ship-service-level`, `ship-country`, `B2B`, `fulfilled-by`) were left as in the source. [If you filled blank `fulfilled-by` with "Not Easy Ship", add that here.]

### Analysis rules decided during cleaning
- **Revenue** excludes cancelled orders.
- **Order Lines** = number of item rows (one order can contain several rows), not unique orders.
- **Units Sold** = sum of `Qty`.
- **March 2022** contains only one day of data, so it is excluded from the monthly trend.
- **Return rate** uses Merchant-fulfilled orders only, because Amazon-fulfilled rows do not record delivery or return outcomes.
- Chart tables on the "Chart Data" sheet were copied from pivot tables as values.


## Business Questions
1. Which states generate the most revenue?
2. How does cancellation vary by state and fulfilment type?
3. How does revenue change month to month?
4. Which categories and sizes earn the most?
5. How large are returns and cancellations by category?

## Key Findings
- Total revenue (excluding cancelled orders): ₹71.6M
- Overall cancellation rate: ~14%
- Merchant-fulfilled cancellation: 17.47% vs Amazon 12.79%


## Files
- `excel\Amazon_Sales_Analysis.xlsx`: cleaned data, pivots, dashboard
- `Amazon_Sales_Dashboard.pdf`: dashboard screenshot
