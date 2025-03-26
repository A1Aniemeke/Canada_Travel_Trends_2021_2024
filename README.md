## Introduction

Curious about how international travel has shifted over time, I explored the volume and nature of travellers entering or returning to Canada. With fluctuating travel regulations, pandemic recovery, and changing mobility trends, this analysis aims to answer:

- How have **traveller volumes** changed from 2021 to 2024?
- Which **provinces** receive the most international travellers?
- What **types of travellers** are most common, and how do they enter the country?

Using official data from **Statistics Canada**, this project offers a breakdown of travel trends by **province, travel type, and traveller characteristics**.

The insights are valuable for **policymakers**, **transportation authorities**, and **businesses** seeking to better understand Canada's evolving travel landscape.



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


