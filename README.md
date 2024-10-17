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
  
</details>

<details>
  <summary>3. Time-Based Insights</summary>

  - Do rentals increase during certain seasons or months?
  
  
</details>

<details>
  <summary>5. Geospatial Analysis</summary>
  
  - Are there common routes (start-to-end station pairs) used by many riders?
  
</details>

<details>
  <summary>6. User Behavior</summary>
  
  - Do users typically return bikes to the same station they rented from?
  
</details>

---

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

---

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

---

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

#### 3.1. Invalid Data: 
A bike can't be returned before it's rented, so trip durations must always be positive. Negative values are likely due to data entry errors or system issues.

#### 3.2. How I Addressed This Issue:
These incorrect records were identified and excluded from the analysis using a filter that only considers trips with valid (positive) durations. This step was necessary to avoid distorting the overall trip duration statistics, such as average and maximum trip times.
```sql
SELECT 
AVG(duration) AS avg_trip_duration_seconds, 
MIN(duration) AS min_trip_duration, 
MAX(duration) AS max_trip_duration 
FROM `bigquery-public-data.london_bicycles.cycle_hire`
WHERE duration >= 0 ;
```
<img width="435" alt="Screenshot 2024-10-11 at 10 45 39" src="https://github.com/user-attachments/assets/a13380fa-78d5-4091-9ca2-8eb6e9015576">

---

#### 4. Stations Running Out of Bikes or Docks
##### 4.1. Stations With No Bikes:
```sql
SELECT 
name, 
COUNT(*) AS times_no_bikes 
FROM `bigquery-public-data.london_bicycles.cycle_stations`
WHERE bikes_count = 0
GROUP BY name
ORDER BY times_no_bikes DESC
limit 10;
```
<img width="385" alt="Screenshot 2024-10-11 at 11 29 48" src="https://github.com/user-attachments/assets/9799d1c8-3821-4293-bcd6-452c8cf4e820">

##### 4.2. Stations With No Empty Docks:
```sql
SELECT 
name, 
COUNT(*) AS times_no_empty_docks 
FROM `bigquery-public-data.london_bicycles.cycle_stations`
WHERE nbEmptyDocks = 0
GROUP BY name
ORDER BY times_no_empty_docks DESC
LIMIT 10;
```
<img width="448" alt="Screenshot 2024-10-11 at 11 33 04" src="https://github.com/user-attachments/assets/6c8a1c7a-d1d3-4e8d-996d-6c4e75442dd4">

---

#### 5. Rentals By Month Or Season
##### 5.1. Rental By Season:
```sql
SELECT 
  CASE 
    WHEN EXTRACT(MONTH FROM start_date) IN (12, 1, 2) THEN 'Winter'
    WHEN EXTRACT(MONTH FROM start_date) IN (3, 4, 5) THEN 'Spring'
    WHEN EXTRACT(MONTH FROM start_date) IN (6, 7, 8) THEN 'Summer'
    WHEN EXTRACT(MONTH FROM start_date) IN (9, 10, 11) THEN 'Autumn'
  END AS season,
  COUNT(rental_id) AS total_rentals
FROM `bigquery-public-data.london_bicycles.cycle_hire`
GROUP BY 
  season;
```
<img width="384" alt="Screenshot 2024-10-11 at 12 47 40" src="https://github.com/user-attachments/assets/4d6fe382-f51d-4aeb-a1b8-526b3ed9df3b">

##### 5.2. Rentals By Month:
```sql
SELECT 
  EXTRACT(MONTH FROM start_date) AS month, 
  COUNT(rental_id) AS total_rentals
FROM `bigquery-public-data.london_bicycles.cycle_hire`
GROUP BY 
  month
ORDER BY 
  month;
```
<img width="312" alt="Screenshot 2024-10-11 at 14 44 44" src="https://github.com/user-attachments/assets/3aeccaa7-f02e-472a-9e27-59e7c34fe734">

---

#### 6. Most Common Routes
```sql
SELECT 
start_station_name, 
end_station_name, 
COUNT(rental_id) AS trip_count 
FROM `bigquery-public-data.london_bicycles.cycle_hire`
GROUP BY start_station_name, end_station_name
ORDER BY trip_count DESC
LIMIT 10;
```
<img width="586" alt="Screenshot 2024-10-11 at 13 12 31" src="https://github.com/user-attachments/assets/e7b58b04-a5fc-46b5-8ea4-65a67a13a9d3">

---

#### 7. Returning to the Same Station
How often do users return bikes to the same station they rented from?
```sql
SELECT 
start_station_name, 
COUNT(rental_id) AS same_station_rentals 
FROM `bigquery-public-data.london_bicycles.cycle_hire`
WHERE start_station_id = end_station_id
GROUP BY start_station_name
ORDER BY same_station_rentals DESC
Limit 10;
```
<img width="490" alt="Screenshot 2024-10-11 at 13 26 46" src="https://github.com/user-attachments/assets/f69e7ff8-5c9b-4548-a2a1-1841ff0ce9e8">

---

### Results

#### Busiest Stations:

Hyde Park, Kings Cross, and Waterloo are the top three busiest starting stations.
The three busiest ending stations are Hyde Park, Kings Cross, and The Borough.

#### Peak Rental Hours:
Peak rental hours occur at 5 PM, 6 PM, and 8 AM, indicating high demand during these times.

#### Average Trip Duration:
The average trip duration is approximately 1,307 seconds (or about 21 minutes).

#### Bike and Dock Availability Issues:
Stations such as Pimlico, Bethnal Green, and Marylebone frequently run out of bikes.
Farringdon and Marylebone often face shortages of docking spaces.

#### Seasonal Trends:
Rentals are significantly higher during the summer months compared to other seasons.

#### Monthly Rentals:
The total number of rentals in June, July, and August exceeds rentals in other months, suggesting a peak during the summer.

#### Most Common Routes:
Hyde Park Corner to Hyde Park Corner
Aquatic Center to Aquatic Center
Albert Gate to Albert Gate

#### Stations with High Return Rates:
Hyde Park Corner, Aquatic Centre, and Albert Gate are the stations with the highest frequency of bikes returning to the same location.

---

### Recommendations

#### Increase Capacity at Busiest Stations:
Expand the number of bikes and docks at Hyde Park, Kings Cross, and Waterloo to meet high demand.

#### Manage Peak Rental Hours:
Deploy additional bikes and staff during peak hours (5 PM, 6 PM, and 8 AM) to accommodate increased rentals.

#### Address Bike and Dock Shortages:
Optimize bike distribution to Pimlico, Bethnal Green, and Marylebone for bikes; ensure Farringdon and Marylebone have adequate docking spaces.

#### Leverage Seasonal Trends:
Offer promotions during summer to maximize rentals during peak seasons.

#### Promote Common Routes:
Highlight popular routes (Hyde Park Corner, Aquatic Center, Albert Gate) in marketing campaigns to encourage usage.

#### Gather User Feedback:
Implement a feedback system to gather insights on availability and user experience, guiding improvements.

---





