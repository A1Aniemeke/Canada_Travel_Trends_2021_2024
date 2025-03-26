## Introduction

Curious about how international travel has shifted over time, I explored the volume and nature of travellers entering or returning to Canada. With fluctuating travel regulations, pandemic recovery, and changing mobility trends, this analysis aims to answer:

- How have **traveller volumes** changed from 2021 to 2024?
- Which **provinces** receive the most international travellers?
- What **types of travellers** are most common, and how do they enter the country?

Using official data from **Statistics Canada**, this project offers a breakdown of travel trends by **province, travel type, and traveller characteristics**.

The insights are valuable for **policymakers**, **transportation authorities**, and **businesses** seeking to better understand Canada's evolving travel landscape.

-

## Overview

This project analyzes data from **Statistics Canada** on international travellers entering or returning to Canada between **2021 and 2024**. The dataset captures entries by **province**, **mode of transportation** (air, land, water), **traveller type** (excursionists vs tourists), and **traveller characteristics** (e.g., Canadian residents returning, U.S. residents, others).

The analysis provides insights into:

- Monthly travel patterns and recovery trends post-2020
- Provincial differences in travel volume
- Distribution of traveller types and their modes of entry
- Key traveller demographics entering Canada



## Dataset Information

- **Source**: [Statistics Canada – Table 24-10-0053-01](https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=2410005301)
- **Title**: *International travellers entering or returning to Canada, by type of transportation and traveller type*
- **Time Frame**: January 2021 to December 2024
- **Update Frequency**: Monthly

### Key Variables
- **Geography**: Province or territory of entry (e.g., Ontario, British Columbia)
- **Traveller Characteristics**: Combines traveller residency and transportation mode (e.g., Canadian residents, air; U.S. residents, land)
- **Traveller Type**: 
  - *Excursionists (same-day)* 
  - *Tourists (overnight)*
- **Month-Year**: Monthly date of travel entry
- **Number of Travellers**: Total number of individuals entering or returning to Canada for that group and month

> *Note: Symbols like "..." in the original dataset were replaced with nulls and cleaned before analysis.*

---

## Data Cleaning & Preprocessing
### Loading the Dataset

To begin the analysis, we loaded the dataset using `pandas`, a powerful Python library for data manipulation. The dataset was sourced from Statistics Canada and contains international traveller information categorized by geography, traveller type, and time period.

```python
import pandas as pd
import numpy as np

# Load the CSV file
file_path = "Travel_data.csv"  # Make sure the file is in the same directory as your notebook
df = pd.read_csv(file_path)

# Preview the first few rows
df.head()
```

### Reshaping the Data

The original dataset was in a wide format, with monthly columns spread across the sheet. To make the data easier to analyze, we reshaped it into a long format using the `melt` function. This transformation consolidated all month columns into two columns: `Month-Year` and `Number of Travellers`.

```python
# Identify date columns (starting from column index 3 onward)
date_columns = df.columns[3:]

# Melt the DataFrame to long format
df_melted = df.melt(
    id_vars=["Geography", "Traveller characteristics", "Traveller type"],
    value_vars=date_columns,
    var_name="Month-Year",
    value_name="Number of Travellers"
)

# Preview the reshaped data
df_melted.head()
```
#### Reshaped Data Preview
| Index | Geography                 | Traveller characteristics                     | Traveller type            | Month-Year | Number of Travellers |
|-------|---------------------------|-----------------------------------------------|----------------------------|-------------|------------------------|
| 0     | Newfoundland and Labrador | United States of America residents, air       | Excursionists (same-day)  | Jan-21      | 151                    |
| 1     | Newfoundland and Labrador | United States of America residents, air       | Tourists (overnight)      | Jan-21      | 17                     |
| 2     | Newfoundland and Labrador | United States of America residents, land      | Excursionists (same-day)  | Jan-21      | ...                    |
| 3     | Newfoundland and Labrador | United States of America residents, land      | Tourists (overnight)      | Jan-21      | ...                    |
| 4     | Newfoundland and Labrador | United States of America residents, water     | Excursionists (same-day)  | Jan-21      | 0                      |


### Clean the "Number of Travellers" Column

To prepare the data for analysis, we cleaned the `"Number of Travellers"` column by handling missing and invalid entries:

```python
# Clean the 'Number of Travellers' column
df_melted["Number of Travellers"] = (
    df_melted["Number of Travellers"]
    .replace("...", np.nan)                  # Replace symbol legend
    .replace(",", "", regex=True)            # Remove commas
    .astype(str)                             # Ensure all are strings
    .str.strip()                             # Remove extra spaces
    .replace("", np.nan)                     # Replace empty strings
    .astype(float)                           # Convert to float
)

# Drop rows with missing values
df_melted.dropna(subset=["Number of Travellers"], inplace=True)

# Check results
df_melted.head()
```


### Convert "Month-Year" to a Proper Date Format

To support time-based analysis, we converted the `"Month-Year"` column into a proper date format using monthly granularity:

```python
# Convert to datetime, then to period (monthly granularity)
df_melted["Month_Year"] = pd.to_datetime(df_melted["Month-Year"], format="%b-%y").dt.to_period('M')

# Drop original 'Month-Year' column if needed
df_melted.drop(columns=["Month-Year"], inplace=True)

# Preview
df_melted.head()
```


### Check for Missing Values

To ensure data completeness, we checked for missing values in each column after cleaning:

```python
# Count missing values in each column
df_melted.isnull().sum()
```
#### Data Cleaning Results
| Feature                    | Missing Values |
|----------------------------|----------------|
| Geography                  | 0              |
| Traveller characteristics  | 0              |
| Traveller type             | 0              |
| Number of Travellers       | 0              |
| Month_Year                 | 0              |

---

## Exploratory Data Analysis (EDA)

This section explores key trends and distributions within the dataset to uncover patterns and insights related to international travel into Canada from 2021 to 2024.

### Key Visualization


### How has international travel to Canada changed over time?

This line chart shows the **monthly trend** of travellers entering or returning to Canada between **January 2021 and December 2024**.

![Total International Travllers ](https://github.com/user-attachments/assets/786fe7c1-9fde-4cbd-a066-890ad3d15f9f)

#### Key Observations:
- There was a **steady recovery** in travel volume after early 2021, likely due to **post-pandemic reopening**.
- Peaks appear consistently during the **summer months** (June–August), indicating **seasonal travel patterns**.
- A **significant surge** occurred mid-2023 and mid-2024, reaching over **9 million travellers** in peak months.
- Travel volume **dipped during winter months**, possibly due to weather and post-holiday slowdowns.



### Which provinces received the highest number of international travellers?

This horizontal bar chart displays the **top 10 provinces** with the highest number of international travellers entering or returning to Canada between **2021 and 2024**.

![Top 10 Provinces by Number of Travellers](https://github.com/user-attachments/assets/73314964-1760-4fa0-8e66-563c9a98f7cd)

#### Key Observations:
- **Ontario** leads by a wide margin, highlighting its role as the **primary entry hub**, likely due to **Pearson International Airport** and dense population.
- **British Columbia** and **Quebec** follow closely, supported by major airports in **Vancouver** and **Montreal**.
- Provinces like **Alberta**, **New Brunswick**, and **Manitoba** show moderate traveller volumes, reflecting regional traffic.
- **Nova Scotia**, **Saskatchewan**, **Yukon**, and **Newfoundland and Labrador** round out the top 10, with significantly fewer travellers.
- This filtered view improves focus by excluding territories with negligible counts.

**Insight**: **Traveller volume is concentrated in a few provinces**, driven by infrastructure, tourism, and economic activity.



### How do traveller types vary over time in Canada?

This line chart highlights the monthly trends of travellers entering or returning to Canada, separated by **Traveller Type**:

![Travel Trend by Traveller Type](https://github.com/user-attachments/assets/023a5d2c-2f67-45a8-8400-ccf47db64412)

#### Key Observations:
- **Tourists (overnight)** consistently make up a larger portion of total travellers compared to **Excursionists (same-day)**.
- There is a clear **seasonal pattern**, with peaks typically occurring mid-year (summer months), which aligns with typical vacation periods.
- Both categories saw a **steady recovery post-2021**, with visible dips during winter months.
- The sharp growth in 2022–2024 reflects a rebound in international mobility, possibly due to **post-COVID border re-openings and relaxed travel restrictions**.

**Insight**: Overnight tourism is a key component of Canada's international travel volume and is more sensitive to seasonal fluctuations.



### What are the top traveller groups entering or returning to Canada?

This bar chart highlights the **top 10 traveller groups** entering or returning to Canada from 2021 to 2024, based on **residency status** and **mode of transport**:

![Traveller Characteristics](https://github.com/user-attachments/assets/9475a932-314b-45d5-b946-982b1874f381)

#### Key Observations:
- The **largest group** is *Canadian residents returning from the U.S. via land*, followed by *U.S. residents entering via land*.
- **Air travel** is the second most dominant mode, especially among **Canadian returnees** and **foreign residents**.
- **Land-based travel** clearly surpasses other modes in total volume.
- Although included, **water travel** remains minimal, indicating **limited seasonal or niche usage**.

**Insight**: **Land crossings**, particularly between Canada and the U.S., remain the **backbone of international mobility**, underscoring deep cross-border integration.
