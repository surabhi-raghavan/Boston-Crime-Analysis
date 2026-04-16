# Boston Crime Analysis

An exploratory data analysis (EDA) of Boston crime incident data using Python and Google Colab. The project covers data ingestion, cleaning, UCR classification, and visualization across crime types and districts.

## Overview

This notebook loads multiple years of Boston crime incident CSVs, merges them into a single dataset, performs cleaning and feature engineering (filling missing UCR classification labels), and produces visualizations to surface patterns in crime frequency across offense types and geographic districts.

## Dataset

- **Source:** Boston Police Department RMS (Records Management System) crime incident reports
- **Format:** Multiple CSV files stored in Google Drive (`BCD_datasets/`)
- **Excluded files:**
  - `RMS_Crime_Incident_Field_Explanation.csv` — data dictionary
  - `RMS_offense_codes.csv` — offense code reference table

## Requirements

```
pandas
numpy
matplotlib
seaborn
google-colab  # for Google Drive mounting
```

## Project Structure

```
BostonCrimeAnalysis.ipynb   # Main analysis notebook
Cleaned_Boston_Crime.csv    # Output: cleaned and labeled dataset
```

## Notebook Walkthrough

### 1. Data Ingestion

- Mounts Google Drive and reads all CSV files from the `BCD_datasets/` folder
- Concatenates them into a single DataFrame

### 2. Exploratory Inspection

- Checks shape, data types, and null counts with `.info()` and `.isna().sum()`
- Previews head and tail rows

### 3. Data Cleaning

| Step                       | Detail                                                               |
| -------------------------- | -------------------------------------------------------------------- |
| Drop irrelevant columns    | Removes `SHOOTING`, `STREET`, `OFFENSE_CODE_GROUP`, `REPORTING_AREA` |
| Drop incomplete rows       | Drops records with null `DISTRICT` values                            |
| Deduplicate                | Keeps first occurrence per `INCIDENT_NUMBER`                         |
| Filter invalid coordinates | Removes rows where `Lat <= 0`                                        |

### 4. UCR Classification (`UCR_PART`)

The `UCR_PART` column has missing values for many offense codes. These are filled through a multi-stage mapping process aligned with FBI Uniform Crime Reporting (UCR) guidelines:

- **Stage 1:** Direct code-level assignments for 100+ specific offense codes into `Part One`, `Part Two`, `Part Three`, or `Other`
- **Stage 2:** Named dictionary mappings for migrated/legacy offense codes (e.g., code 650 → `Part One`, code 2950 → `Part Three`)
- **Stage 3:** Propagation — infers remaining null values from existing labeled rows with the same offense code
- **Output:** Zero null `UCR_PART` values

UCR classification tiers:

- **Part One** — serious violent and property crimes (homicide, robbery, burglary, larceny, auto theft, assault)
- **Part Two** — less serious crimes (fraud, drug offenses, weapons violations, vandalism)
- **Part Three** — administrative/investigative incidents (death investigation, medical assist, property investigations)
- **Other** — miscellaneous/evidence tracker incidents

### 5. Export

Cleaned dataset is saved to `Cleaned_Boston_Crime.csv`.

### 6. Visualizations

**Top 10 Most Committed Crimes**
Horizontal bar chart showing the 10 most frequent offense descriptions across the entire dataset.

**Crime Count by District**
Bar chart showing total incident volume per Boston police district.

## Output

| File                       | Description                                        |
| -------------------------- | -------------------------------------------------- |
| `Cleaned_Boston_Crime.csv` | Fully cleaned dataset with all UCR labels resolved |

## To Do

- [ ] **Interactive website** — build a dashboard (Streamlit or Gradio) with filterable charts, district maps, and time-based drill-downs
- [ ] **Machine learning** — crime prediction model (e.g., classify UCR part or predict incident hotspots by district/time)

## Notes

- The notebook is designed for Google Colab; the Drive mount cell (`drive.mount(...)`) should be run first before loading data.
- UCR mapping was applied manually in stages to maximize coverage rather than dropping unmapped rows, preserving dataset completeness.
