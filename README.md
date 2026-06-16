# NYC Public Schools SAT Performance Analysis

A data analysis project exploring SAT performance across New York City public schools using `pandas`.

🔗 [View the notebook on DataCamp](https://www.datacamp.com/datalab/w/c734fc91-a620-4175-aee6-26e906b0bdf5/edit/nyc_schools_sat_analysis.ipynb)

## Overview

This project analyzes SAT score data from NYC public schools to answer three questions:

1. Which schools have the best math results?
2. Which 10 schools perform best based on combined SAT scores?
3. Which borough has the largest standard deviation in combined SAT scores?

## Dataset

The analysis uses `schools.csv`, which contains the following columns:

| Column | Description |
|---|---|
| `school_name` | Name of the school |
| `borough` | NYC borough the school is located in |
| `building_code` | Code of the building the school is in |
| `average_math` | Average SAT math score |
| `average_reading` | Average SAT reading score |
| `average_writing` | Average SAT writing score |
| `percent_tested` | Percentage of students who took the SAT |

## Analysis

### 1. Best math results

Schools are considered to have the best math results if their average math score is at least 80% of the maximum possible score (800), i.e. a score of 640 or higher.

```python
best_math_schools = schools[schools["average_math"] >= 640][["school_name", "average_math"]].sort_values("average_math", ascending=False)
```

**Output:** a DataFrame with `school_name` and `average_math`, sorted in descending order.

### 2. Top 10 schools by combined SAT score

A new `total_SAT` column is created by summing the math, reading, and writing averages, then the top 10 schools are selected.

```python
schools["total_SAT"] = schools["average_math"] + schools["average_reading"] + schools["average_writing"]
top_10_schools = schools.sort_values(by="total_SAT", ascending=False).head(10)[["school_name", "total_SAT"]]
```

**Output:** a DataFrame with `school_name` and `total_SAT` for the top 10 schools, sorted in descending order.

### 3. Borough with the largest standard deviation in SAT scores

Schools are grouped by borough, and the count, mean, and standard deviation of `total_SAT` are calculated for each. The borough with the highest standard deviation is selected.

```python
boroughs = schools.groupby("borough")["total_SAT"].agg(["count", "mean", "std"]).round(2)

largest_std_dev = boroughs[boroughs["std"] == boroughs["std"].max()]
largest_std_dev = largest_std_dev.rename(columns={"count": "num_schools", "mean": "average_SAT", "std": "std_SAT"})
largest_std_dev.reset_index(inplace=True)
```

**Output:** a single-row DataFrame with `borough`, `num_schools`, `average_SAT`, and `std_SAT`, all numeric values rounded to two decimal places.

## Full Code

```python
import pandas as pd

# Read in the data
schools = pd.read_csv("schools.csv")

# Preview the data
schools.head()

# Which schools have the best math results?
best_math_schools = schools[schools["average_math"] >= 640][["school_name", "average_math"]].sort_values("average_math", ascending=False)

# Calculate total SAT score for each school
schools["total_SAT"] = schools["average_math"] + schools["average_reading"] + schools["average_writing"]

# Top 10 schools by total_SAT
top_10_schools = schools.sort_values(by="total_SAT", ascending=False).head(10)[["school_name", "total_SAT"]]

# Which borough has the highest standard deviation for total_SAT?
boroughs = schools.groupby("borough")["total_SAT"].agg(["count", "mean", "std"]).round(2)

largest_std_dev = boroughs[boroughs["std"] == boroughs["std"].max()]
largest_std_dev = largest_std_dev.rename(columns={"count": "num_schools", "mean": "average_SAT", "std": "std_SAT"})
largest_std_dev.reset_index(inplace=True)

print(best_math_schools)
print(top_10_schools)
print(largest_std_dev)
```

## Requirements

- Python 3
- pandas

```bash
pip install pandas
```

## Usage

1. Place `schools.csv` in the project directory.
2. Run the script:

```bash
python analysis.py
```

## License

This project is open source and available under the MIT License.
