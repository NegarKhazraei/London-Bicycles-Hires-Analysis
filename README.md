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

<details>
  <summary>1. Usage Patterns</summary>
  
  - What are the busiest stations for starting and ending bike trips?
  - When are the peak hours, days, and months for bike rentals?
  - What is the average trip duration?
  
</details>

<details>
  <summary>2. Station Performance</summary>
  
  - Which stations frequently run out of bikes or docks (empty/full)?
  - Do temporary stations perform differently from permanent ones?
  
</details>

<details>
  <summary>3. Time-Based Insights</summary>
  
  - Are trip durations longer during weekends or off-peak hours?
  - Do rentals increase during certain seasons or months?
  
</details>

<details>
  <summary>4. Bike and Trip Insights</summary>
  
  - Which bike models are rented the most, and do they impact trip durations?
  - How often are specific bikes rented, and are some bikes used more frequently?
  
</details>

<details>
  <summary>5. Geospatial Analysis</summary>
  
  - Are there common routes (start-to-end station pairs) used by many riders?
  
</details>

<details>
  <summary>6. User Behavior</summary>
  
  - Do users typically return bikes to the same station they rented from?
  - Are there differences in weekday vs. weekend usage?
  
</details>

<details>
  <summary>7. Optimization and Recommendations</summary>
  
  - How can bike redistribution be improved based on station demand?
  - Should new stations be added in high-demand areas or underperforming ones relocated?
  
</details>

### Data Analysis
#### 1. Busiest Stations (Start and End)
```sql
SELECT 
start_station_name, 
COUNT(rental_id) AS trips_started 
FROM `bigquery-public-data.london_bicycles.cycle_hire`
GROUP BY start_station_name
ORDER BY trips_started DESC
LIMIT 10;
```
<img width="388" alt="Screenshot 2024-10-10 at 20 10 57" src="https://github.com/user-attachments/assets/fbc52743-b83d-4bfc-8044-da8e983c26a3">

```sql
SELECT 
end_station_name, 
COUNT(rental_id) AS trips_ended 
FROM `bigquery-public-data.london_bicycles.cycle_hire`
GROUP BY end_station_name
ORDER BY trips_ended DESC
LIMIT 10;
```
<img width="386" alt="Screenshot 2024-10-10 at 20 13 00" src="https://github.com/user-attachments/assets/9a1c6a05-2e0e-4992-94c8-1b261ae4949f">

#### 2. Peak Hours for Rentals
When are the peak hours for bike rentals?
```sql
SELECT 
EXTRACT(HOUR FROM start_date) AS hour_of_day, 
COUNT(rental_id) AS total_rentals 
FROM `bigquery-public-data.london_bicycles.cycle_hire`
GROUP BY hour_of_day
ORDER BY total_rentals DESC;
```
<img width="308" alt="Screenshot 2024-10-10 at 20 37 06" src="https://github.com/user-attachments/assets/915f2b2f-8735-4c5c-aa4a-58f2e03e7846">

#### 3. Average Trip Duration
What is the average trip duration?
```sql
SELECT 
AVG(duration) AS avg_trip_duration_seconds, 
MIN(duration) AS min_trip_duration, 
MAX(duration) AS max_trip_duration 
FROM `bigquery-public-data.london_bicycles.cycle_hire`;
```
<img width="437" alt="Screenshot 2024-10-10 at 21 33 38" src="https://github.com/user-attachments/assets/9a194661-2e05-4b27-ae56-ba274064659c">

#### Invalid Data: 
A bike can't be returned before it's rented, so trip durations must always be positive. Negative values are likely due to data entry errors or system issues.





### Results
1. There are more than 26 million rows with bike trips that are 20 minutes or longer!

### Recommendations

### Limitations

😄



