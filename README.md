# How Well-Resourced Are Sydney's Suburbs? A Spatial Data Analysis

Which suburbs in Greater Sydney have the best access to businesses, public transport, schools and local facilities, and do better-resourced areas have higher incomes? This project combines several public datasets in a PostgreSQL/PostGIS database to score and compare SA2 regions across three SA4 zones.

📄 **Full report:** [`Report.pdf`](Report.pdf)

## Overview

We built a spatial database integrating business, income, population, public transport, school catchment and points-of-interest data. We then created a composite **"well-resourced" score** for each SA2 region, focusing on six essential industries (Education, Retail, Health, Professional Services, Hospitality, and Arts), and compared scores across three SA4 regions:

- Sydney – Parramatta
- Sydney – Inner South West
- Sydney – City and Inner South

## Data

| Dataset | Description |
|---|---|
| Businesses | Business counts by industry and revenue per SA2 |
| Income | Median income per SA2 (missing values stored as NULL, not 0, to avoid skewing results) |
| Population | Population by age group per SA2 |
| Stops | Public transport stops across Greater Sydney |
| SA2 boundaries | Shapefile of statistical area boundaries |
| School catchments | Primary, secondary and future school catchment shapefiles |
| Points of interest | Local facilities used in the score |

## Database design

- **9 tables** in PostgreSQL with PostGIS, linked by `sa2_code` foreign keys for referential integrity
- **Spatial joins** with PostGIS `ST_Within` to assign transport stops, points of interest and school catchments to SA2 regions
- **Indexes** to speed up queries: attribute indexes on `sa2_code`, and **GiST spatial indexes** on geometry columns

## Building the score

Each component (business density, transport access, school proximity, nearby facilities) is on a different scale, so we normalised them before combining:

1. **Z-score normalisation**, so no single component dominates
2. **Winsorized z-scores**, capping the top and bottom 5% to limit the effect of outliers
3. **Sigmoid transformation**, compressing the combined score to a 0–1 range for easy comparison

## Key findings

- **Each region has its own strengths:** Parramatta stands out for public transport, Inner South West for business density and facilities, while City and Inner South scores poorly on transport.
- **Average scores are similar across regions** (0.45–0.48), but **every region contains both highly and poorly resourced suburbs**, showing large inequality within regions.
- **Better-resourced areas aren't the wealthiest.** There's a statistically significant **negative correlation** between score and median income in Parramatta (Pearson r = −0.45, Spearman r = −0.42) and Inner South West (Pearson r = −0.39, Spearman r = −0.36), both p < 0.05. This may reflect public investment in lower-income suburbs, or wealthier areas relying more on private resources.
- **In City and Inner South there's no clear relationship** (r ≈ −0.05, p > 0.3), with affluent and disadvantaged suburbs receiving similar levels of resources.

## Methods and tools

Python (Jupyter), SQL, PostgreSQL, PostGIS, spatial joins and indexing, z-score and winsorized normalisation, Pearson and Spearman correlation, map and statistical visualisation

## Team

DATA2001 group assignment (Class 15, Group 3), University of Sydney.

Serafina Aurelia, Nathanael Prabowo, Vardaan Anand

**My contribution (Serafina Aurelia):** [describe your part, e.g. database design, the scoring formula, correlation analysis, visualisations or report sections]
