# Healthcare Provider Dashboard

A Power BI report that turns raw hospital visit records into an at-a-glance view of billing, procedures, departments, and diagnoses. This repository is a portfolio project documenting what I learned building the dashboard end to end, from raw CSVs to a polished, interactive report.

![Healthcare Provider Dashboard](Healthcare%20Provider%20Dashboard.PNG)

## The Dashboard

The report answers questions a hospital administrator would actually ask:

- **How much are we billing, and where does the cost come from?** Six KPI cards break total billing ($3.4M) down into medication, treatment, insurance coverage, out-of-pocket, and room charges, each paired with a per-visit average.
- **Which procedures and departments drive revenue?** A ranked column chart shows billing by procedure (X-Ray, CT Scan, MRI, etc.), and a bar chart ranks departments by total billing with their percentage share.
- **Where are our patients?** A city table ranks total billing by location.
- **How is care delivered per diagnosis?** A 100% stacked bar shows the mix of Emergency / Inpatient / Outpatient service types for each diagnosis.
- **Slice everything on demand.** A slide-out panel filters the entire report by patient race and by year/quarter, then tucks away to keep the canvas clean.

![Filter panel](Healthcare%20Provider%20Dashboard%20Filter.PNG)

## The Data Model

The dataset is a classic **star schema**: one fact table surrounded by descriptive dimension tables.

![Model view](Healthcare%20ModelView.PNG)

| Table | Role | Key columns |
|---|---|---|
| `visits` | **Fact**, one row per patient visit | Date of Visit, Treatment/Medication Cost, Insurance Coverage, Room Charges, Service Type, and foreign keys to every dimension |
| `patients` | Dimension | Patient ID, Name, Gender, Age, Race, City ID |
| `providers` | Dimension | Provider ID, Name, Gender, Nationality, Age, Image |
| `departments` | Dimension | Department ID, Department |
| `diagnoses` | Dimension | Diagnosis ID, Diagnosis |
| `procedures` | Dimension | Procedure ID, Procedure |
| `insurance` | Dimension | Insurance ID, Insurance Provider |
| `cities` | Dimension | City ID, City, State |
| `DateTable` | Date dimension | Date, Month, Month number (marked as the report's date table) |

The `visits` table connects to each dimension through its ID column, forming one-to-many relationships that let a single visit be sliced by patient, provider, department, diagnosis, procedure, insurer, or geography. A dedicated `DateTable` joins to Date of Visit and drives the year/quarter filtering, which is the recommended pattern for reliable time-based analysis in Power BI.

## What I Learned

### Data modeling
- **Building a star schema from flat files.** I imported eight separate CSVs and wired up the relationships in the model view rather than flattening everything into one giant table, keeping the fact table narrow and the dimensions reusable.
- **Understanding cardinality and filter direction.** Each dimension is on the "one" side and `visits` on the "many" side, so filters flow outward from a dimension into the fact table. Getting this right is what makes a single slicer update every visual on the page.
- **Choosing the right grain.** Recognizing that "one row = one visit" is the fact grain shaped every measure and aggregation that followed.
- **Adding a dedicated date table.** Instead of relying on the raw Date of Visit column, I created a separate `DateTable` and marked it as the report's date table, so year/quarter drill-down and time intelligence work reliably.

### Data preparation (Power Query)
- Cleaning and type-casting imported columns (dates, currency, whole numbers) so aggregations and the date hierarchy behave correctly.
- Handling missing and `N/A` values (for example, room type and admit/discharge dates for outpatient visits) so they don't distort totals.

### DAX & measures
- Writing explicit measures for the KPI cards: total billing components alongside their **per-visit averages** (for example, `Average Amount = $674.86` vs. total `$3.4M`).
- Understanding the difference between a summed total and an average of a measure, and why an average per visit tells a different story than a grand total.

### Report design & UX
- **KPI cards** for headline numbers, with a total-plus-average pattern that adds context without clutter.
- **Choosing the right visual for the question**: ranked columns for "which is biggest," a 100% stacked bar for "what's the mix," a table for precise city-level values.
- **A slide-out filter panel** built with bookmarks and buttons (Open/Close Filter), an interaction pattern that keeps advanced filtering available without permanently taking up canvas space.
- **Consistent visual theme**: a cohesive blue palette, rounded card containers, and clear typographic hierarchy so the report reads as one designed surface rather than a pile of charts.
- **Cross-filtering**: clicking a procedure, department, or city filters the rest of the page, turning the report into an exploratory tool instead of a static snapshot.

### Working from requirements
- The dataset shipped with a `Dashboard Requirements.pdf`. I practiced translating a written brief into concrete visuals and measures: deciding what to show, how to group it, and which interactions matter.

## Repository Contents

| File | Description |
|---|---|
| `Healthcare Provider Dataset.pbix` | The Power BI report file (open in Power BI Desktop) |
| `Healthcare Provide Dataset.zip` | Source data: the eight CSVs, provider images, and the requirements PDF |
| `Healthcare Provider Dashboard.PNG` | Screenshot of the main report page |
| `Healthcare Provider Dashboard Filter.PNG` | Screenshot with the slide-out filter panel open |
| `Healthcare ModelView.PNG` | Screenshot of the data model (star schema) |

## How to Open

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free).
2. Open `Healthcare Provider Dataset.pbix`.
3. If prompted for data sources, unzip `Healthcare Provide Dataset.zip` and repoint the queries to the extracted CSV folder.

---

*Portfolio project. The dataset is fictional and used for learning and demonstration purposes.*
