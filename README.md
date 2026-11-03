# NCR Ride Bookings Analysis Dashboard

An interactive **Power BI dashboard** built using the NCR ride-bookings dataset. The project explores booking performance, ride volume, vehicle usage, revenue, cancellations, and customer/driver ratings.

> **Project files**
> - `ncr_ride_bookings.csv` — source dataset
> - `Uber.pbix` — Power BI report and data model

## 1. Project Overview

The objective of this project is to turn raw ride-booking records into useful business insights. Instead of looking at individual bookings one by one, the report summarizes the data through KPIs, charts, tables, and filters.

The dashboard is organized into these report pages:

| Report page | Purpose |
|---|---|
| Home Page | Landing/navigation page for the report |
| Overall | High-level booking performance, ride volume over time, booking-status breakdown, and total bookings |
| Vehicle Type | Comparison of vehicle categories using KPI cards and vehicle-related metrics |
| Revenue | Revenue/payment-method analysis, ride-distance distribution, and top-customer information |
| Cancellation | Customer and driver cancellations, cancellation-related KPIs, and cancellation breakdowns |
| Ratings | Rating-related KPI cards for understanding service experience |

## 2. Dataset Description

The source file contains **150,000 rows and 21 columns**. Each row represents a ride-booking record.

### Main fields

| Field | Description |
|---|---|
| `Date`, `Time` | Date and time associated with the booking |
| `Booking ID` | Unique booking reference |
| `Booking Status` | Outcome/status of the booking |
| `Customer ID` | Customer reference |
| `Vehicle Type` | Vehicle category selected/requested |
| `Pickup Location`, `Drop Location` | Ride origin and destination |
| `Avg VTAT` | Average vehicle time of arrival |
| `Avg CTAT` | Average customer time of arrival |
| `Cancelled Rides by Customer` | Customer-cancellation indicator/count field |
| `Reason for cancelling by Customer` | Customer cancellation reason |
| `Cancelled Rides by Driver` | Driver-cancellation indicator/count field |
| `Driver Cancellation Reason` | Driver cancellation reason |
| `Incomplete Rides` | Incomplete-ride indicator/count field |
| `Incomplete Rides Reason` | Reason a ride was incomplete |
| `Booking Value` | Booking amount/value |
| `Ride Distance` | Distance associated with the ride |
| `Driver Ratings`, `Customer Rating` | Driver and customer rating fields |
| `Payment Method` | Payment method used |

The dataset includes completed, customer-cancelled, driver-cancelled, incomplete, and no-driver-found bookings. Many fields are blank when they do not apply to a particular booking outcome. For example, a booking that never reached completion may not have a booking value, ride distance, or rating.

**Important:** A blank value does not automatically mean zero. In this dataset, blanks can mean that a field was not applicable or was not recorded. The analysis should preserve that distinction unless a specific business rule justifies replacing blanks with zero.

## 3. Tools and Technologies

- **Microsoft Power BI Desktop** — report development and visualization
- **Power Query Editor** — data inspection, cleaning, and preparation
- **Power BI Data Model** — organizing fields and measures for analysis
- **DAX (Data Analysis Expressions)** — calculated measures and KPIs
- **CSV** — source data format
- **Git and GitHub** — version control and project presentation

## 4. Project Workflow

The project follows a typical business-intelligence workflow:

1. Import the CSV dataset into Power BI.
2. Inspect the source columns, data types, and missing values in Power Query Editor.
3. Clean and prepare the data for analysis.
4. Apply the prepared data to the Power BI data model.
5. Create DAX measures for key business metrics.
6. Build report pages and visualizations.
7. Add slicers and navigation to make the report interactive.
8. Validate the figures and publish the project files to GitHub.

## 5. Data Preparation in Power Query Editor

Power Query is the ETL (Extract, Transform, Load) layer of Power BI. It helps prepare raw data before it is loaded into the analytical model.

### 5.1 Connect to the source

1. Open Power BI Desktop.
2. Select **Home → Get Data → Text/CSV**.
3. Choose `ncr_ride_bookings.csv`.
4. Preview the data and select **Transform Data** to open Power Query Editor.

At this stage, check that the expected columns are present and that the first row is being interpreted as the column headers.

### 5.2 Inspect the data

Before making transformations, inspect:

- Column names and spelling
- Data types
- Blank and null values
- Duplicate booking IDs
- Unexpected text values or inconsistent category labels
- Numeric columns that may have been imported as text
- Date and time fields

This inspection is important because a dashboard is only as reliable as the data behind it.

### 5.3 Set appropriate data types

Use data types that match the meaning of each column:

| Column type | Suitable Power Query type |
|---|---|
| Booking date | Date |
| Booking time | Time |
| Booking and customer IDs | Text |
| Booking status, vehicle type, locations, reasons, payment method | Text |
| VTAT, CTAT, booking value, ride distance, ratings | Decimal Number |
| Indicator/count fields | Whole Number or Decimal Number, depending on their actual values |

IDs should normally remain text, even if they contain digits. They are identifiers, not quantities to be added or averaged.

### 5.4 Handle missing values carefully

The source contains missing values in several fields, including arrival times, cancellation details, booking value, ride distance, ratings, and payment method.

A good cleaning decision depends on the meaning of the field:

- Keep a blank rating blank when no rating was submitted.
- Keep booking value blank when no applicable transaction value exists.
- Keep cancellation reasons blank for bookings that were not cancelled.
- Do not replace every null with zero automatically.
- If a business rule requires a replacement, document the rule and apply it consistently.

This avoids misleading averages and totals.

### 5.5 Check duplicates and consistency

Check whether `Booking ID` is unique. If duplicate records exist, investigate them before removing anything; duplicates may represent repeated source records or legitimate multiple records, depending on the data design.

Also check category values such as `Booking Status`, `Vehicle Type`, and `Payment Method` for spelling differences, extra spaces, or inconsistent labels. Standardize values only when they clearly represent the same category.

### 5.6 Apply and load

After the required transformations:

1. Review the Applied Steps pane.
2. Confirm that no transformation has unintentionally removed valid bookings.
3. Select **Close & Apply**.
4. Verify the loaded table and data types in Power BI.

**Reproducibility note:** The exact transformation list is stored in the Power BI query. This README describes the data-preparation process and the checks that should be performed; it does not claim that every optional cleaning operation above was applied in the supplied PBIX.

## 6. Data Modeling

Data modeling is the process of organizing data so that calculations and visuals behave consistently.

For this project, the ride-booking table contains the booking-level information used by the report. Before creating measures, confirm:

- The table name used in the model
- The data type of each measure input
- Whether `Booking ID` is unique
- Whether date analysis needs a separate calendar table
- Whether any relationships are required

### Why use measures?

A DAX measure calculates a result dynamically in the current filter context. For example, when a user selects a date or vehicle type, a correctly written measure recalculates for that selection.

Measures are preferable to manually entering KPI values because they keep the report responsive to slicers and filters.

## 7. DAX Measures — Concepts and Examples

The supplied report contains KPI cards for metrics such as total bookings, successful bookings, cancelled bookings, and cancellation rate. The following are **illustrative DAX patterns** for this dataset. The actual measure names and formulas in your PBIX may differ; compare these examples with your own measures before documenting them as the exact formulas used.

Assume the model table is named `'ncr_ride_bookings'`. If your table has a different name, replace it in the formulas.

### 7.1 Total Bookings

```DAX
Total Bookings =
COUNTROWS('ncr_ride_bookings')
```

Counts the booking records in the current filter context. If one row represents one booking, this gives the total number of bookings. If duplicate booking IDs are possible, consider using `DISTINCTCOUNT` on `Booking ID` instead.

### 7.2 Successful / Completed Bookings

```DAX
Completed Bookings =
CALCULATE(
    COUNTROWS('ncr_ride_bookings'),
    'ncr_ride_bookings'[Booking Status] = "Completed"
)
```

`CALCULATE` changes the filter context so that only completed bookings are counted.

### 7.3 Cancelled Bookings

```DAX
Cancelled Bookings =
CALCULATE(
    COUNTROWS('ncr_ride_bookings'),
    'ncr_ride_bookings'[Booking Status]
        IN {"Cancelled by Customer", "Cancelled by Driver"}
)
```

Counts bookings cancelled by either the customer or the driver. This definition should be kept consistent throughout the report.

### 7.4 Cancellation Rate

```DAX
Cancellation Rate =
DIVIDE(
    [Cancelled Bookings],
    [Total Bookings],
    0
)
```

`DIVIDE` safely handles a zero denominator. Format this measure as a percentage in Power BI. The denominator here is all bookings, not only bookings with a driver assigned.

### 7.5 Total Booking Value

```DAX
Total Booking Value =
SUM('ncr_ride_bookings'[Booking Value])
```

Adds the available booking values in the current filter context. Check the business definition of revenue before describing this measure as recognized revenue.

### 7.6 Average Ride Distance

```DAX
Average Ride Distance =
AVERAGE('ncr_ride_bookings'[Ride Distance])
```

Calculates the average of nonblank ride-distance values. If distance is meaningful only for completed rides, create a completed-rides-only version for a more precise operational comparison.

### 7.7 Average Driver Rating

```DAX
Average Driver Rating =
AVERAGE('ncr_ride_bookings'[Driver Ratings])
```

Averages recorded driver ratings. Blank ratings are excluded by `AVERAGE`.

### 7.8 Average Customer Rating

```DAX
Average Customer Rating =
AVERAGE('ncr_ride_bookings'[Customer Rating])
```

Averages recorded customer ratings.

### Understanding the DAX functions

- `COUNTROWS` counts rows in a table.
- `DISTINCTCOUNT` counts unique values in a column.
- `CALCULATE` evaluates an expression under modified filter conditions.
- `SUM` adds numeric values.
- `AVERAGE` calculates the mean of nonblank numeric values.
- `DIVIDE` performs safe division and can return an alternate result when the denominator is zero.

## 8. Dashboard Pages and Visual Analysis

### 8.1 Home Page

The Home Page acts as the report's landing and navigation screen. Navigation buttons help users move between the report sections without searching through individual pages.

### 8.2 Overall Analysis

The Overall page provides a high-level view of ride-booking performance.

It includes:

- **Total Bookings KPI:** the overall number of booking records.
- **Ride Volume Over Time:** a line chart showing how booking volume changes over the selected date period.
- **Booking Status Breakdown:** a pie chart showing the distribution of booking outcomes.
- **Date slicer:** allows users to focus on a selected date or date range.

Business questions:
- How does ride demand change over time?
- Which booking status occurs most frequently?
- Are there periods with unusually high or low booking volume?

### 8.3 Vehicle Type Analysis

The Vehicle Type page compares the different vehicle categories in the dataset. It uses KPI cards and vehicle-related metrics to support comparisons between categories.

Business questions:
- Which vehicle categories receive the most bookings?
- How do booking outcomes vary by vehicle category?
- Which vehicle categories contribute more to the selected performance metrics?

Use the page's filters and cards together rather than interpreting one metric in isolation.

### 8.4 Revenue Analysis

The Revenue page includes:

- Revenue by Payment Method
- Ride Distance Distribution
- Top 5 Customers
- Date filtering

Business questions:
- Which payment methods account for more booking value?
- How are ride distances distributed?
- Which customers have the highest booking value under the report's ranking logic?

A top-customer table is a ranking, not proof of customer profitability or long-term value.

### 8.5 Cancellation Analysis

The Cancellation page focuses on unsuccessful bookings and cancellation patterns. It includes:

- Customer cancellation breakdown
- Driver cancellation breakdown
- Total Bookings KPI
- Successful Bookings KPI
- Cancelled Bookings KPI
- Cancellation Rate KPI
- Date filtering

Business questions:
- How much of the booking volume is cancelled?
- Are cancellations more associated with customers or drivers?
- What operational reasons may need further investigation?

Cancellation reasons should be interpreted using only records where the corresponding reason is applicable and recorded.

### 8.6 Ratings Analysis

The Ratings page presents rating-related KPI cards. Ratings help assess service experience, but averages should be interpreted alongside the number of ratings available. A high average based on very few records may not represent the overall customer experience.

## 9. Interactivity and Filter Context

The report includes date slicers and page navigation. A slicer filters the visuals connected to it. DAX measures are recalculated under the selected filter context.

For example, selecting a date range can update booking totals, status distributions, revenue, and cancellation metrics. Selecting a vehicle category can narrow the analysis to that category if the visual and slicer interactions are configured accordingly.

When validating the report, test slicers on each page and confirm that all intended visuals respond correctly.

## 10. Key Analytical Considerations

1. **Booking status matters:** completed, cancelled, incomplete, and no-driver-found bookings represent different operational outcomes.
2. **Nulls are meaningful:** missing values often indicate that a field does not apply to a booking.
3. **Avoid misleading averages:** average ratings, arrival times, and ride distances should use the appropriate population.
4. **Define cancellation consistently:** state whether the cancellation rate includes customer cancellations, driver cancellations, or both.
5. **Check the grain:** confirm whether one row equals one booking before using `COUNTROWS`.
6. **Validate totals:** compare KPI totals with filtered source records and verify that categories add up as expected.

## 11. How to Open the Project

1. Download or clone this repository.
2. Keep `Uber.pbix` and `ncr_ride_bookings.csv` in the expected project location.
3. Open `Uber.pbix` using Power BI Desktop.
4. If Power BI reports that the source file cannot be found, open **Transform data → Data source settings** (or edit the source step in Power Query) and point the query to the local CSV file.
5. Select **Refresh**.
6. Review each report page and test the slicers.

## 12. Repository Structure

```text
ncr-ride-bookings-dashboard/
│
├── README.md
├── ncr_ride_bookings.csv
└── Uber.pbix
```

## 13. Skills Demonstrated

- Data import and exploration
- Data cleaning and transformation using Power Query
- Data types, null handling, and data-quality checks
- Data modeling concepts
- DAX measures and filter context
- KPI development
- Interactive report design
- Time-based and category-based analysis
- Business insight communication
- GitHub project documentation

## 14. Future Improvements

- Add a dedicated Calendar table for robust time intelligence.
- Add month-over-month and year-over-year booking comparisons.
- Build a drill-through page for individual booking details.
- Add a decomposition of cancellation reasons by vehicle type and location.
- Add geographic analysis of pickup and drop locations.
- Add a data-quality page showing missing values and duplicate IDs.
- Document the exact DAX measures and Power Query Applied Steps used in the final PBIX.

## 15. Author

**Project:** NCR Ride Bookings Analysis Dashboard  
**Tool:** Microsoft Power BI  
**Dataset:** NCR Ride Bookings CSV

---

*This README documents the supplied report structure and dataset. The DAX snippets are examples for learning and should be checked against the actual measures in the PBIX before being represented as the exact formulas used in the report.*
