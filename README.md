# London Bicycles Hires Analysis

## Table Of Contents
- [Project Overiew](#project-overview)
- [Data Source](#data-source)

### Project Overview

This project analyzes the London Bicycle Hires dataset to uncover trends in bike usage, focusing on time of day, seasonality, and weather impacts. Key findings include peak usage periods, seasonal variations, and the effect of temperature and rainfall on ride frequency. The results provide insights for improving urban transportation planning.

### Data Source
The London Bicycle Hires dataset was sourced from the Public Datasets available on Google BigQuery.

### Tools
- Google Big Query, SQL Work Space [See Here](https://data.london.gov.uk)

### Exploratory Data Analysis

EDA involved exploring the dataset to answer key questions, such as:

- How many bike trips lasted for 20 minutes or longer?
- What are the names of the stations that bike_id 1710 started from?
- How many bike_ids have ended at "Moor Street, Soho"?
- What is the station_id for "Canton Street, Poplar"?
- What is the name of the station whose ID is 111?
- How many distinct bike_ids had trip durations greater than 2400 seconds (or 40 minutes)?

### Data Analysis
```sql
SELECT COUNT(*) AS number_of_trips
FROM cycle_hire table
WHERE duration >= 1200 ;
```

### Results
1. There are more than 26 million rows with bike trips that are 20 minutes or longer!

### Recommendations

### Limitations

😄



