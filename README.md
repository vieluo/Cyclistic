# Cyclistic
A show case project from Google Data Analytics Professional Certificate.

The data used in this case study is sourced from Motivate International Inc under [this agreement.](https://ride.divvybikes.com/data-license-agreement)

This analysis is conducted by SQL.
## Introduction
Welcome to the Cyclistic bike-share analysis case study! In this case study, you will perform many real-world tasks of a junior
data analyst. You will work for a fictional company, Cyclistic, and meet different characters and team members. 

## Question
How do annual members (Subscriber) and casual riders (Customer) use Cyclistic bikes differently?

## Analysis
In this analysis process I only used the dataset from 2019 Q1 and 2019 Q4 because the other set is larger than 100MB which is not allowed to be upload in my BigQuery sandbox acount.

1. What is the Maximum, Minimum, Average trip duration different by subscriber and casual riders, how many trips accomplished by member and casual riders.

```sql
SELECT usertype, MAX(tripduration) AS Max_duration, MIN(tripduration) AS Min_duration, AVG(tripduration) AS Avg_duration, Count(usertype) AS No_user
FROM 
 (SELECT usertype, tripduration
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT usertype, tripduration
 FROM my-project-1-362921.cycalistic.2019q4) AS subtable
 GROUP BY usertype
```

| usertype | Max_duration | Min_duration | Avg_duration | No_user
|---|---|---|---|---|
| Subscriber | 6096430.0 | 61.0 | 779.99030396929129 | 939766
| Customer | 10628400.0 | 61.0 | 3674.4601219879642 | 129357

2. From which station the subscriber and casual riders usually start with.

```sql
SELECT usertype, from_station_name, Count(from_station_name) AS No_start_station
FROM 
 (SELECT usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q4) AS subtable
 WHERE usertype = 'Subscriber'
 GROUP BY usertype,from_station_name
 ORDER BY No_start_station desc
 Limit 3
 ```
| usertype | from_station_name | No_start_station
|---|---|---
Subscriber | Canal St & Adams St | 18511
Subscriber | Clinton St & Washington Blvd | 17027
Subscriber | Clinton St & Madison St | 16242

```sql
SELECT usertype, from_station_name, Count(from_station_name) AS No_start_station
FROM 
 (SELECT usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q4) AS subtable
 WHERE usertype = 'Customer'
 GROUP BY usertype,from_station_name
 ORDER BY No_start_station desc
 Limit 3
 ```
 | usertype | from_station_name | No_start_station
|---|---|---
Customer | Streeter Dr & Grand Ave | 6210
Customer | Lake Shore Dr & Monroe St | 5995
Customer | Shedd Aquarium | 3685

3. Which station the subscriber and casual riders usually end with.

```sql
SELECT usertype, to_station_name, Count(to_station_name) AS No_end_station
FROM 
 (SELECT usertype, to_station_name
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT usertype, to_station_name
 FROM my-project-1-362921.cycalistic.2019q4) AS subtable
 WHERE usertype = 'Subscriber'
 GROUP BY usertype,to_station_name
 ORDER BY No_end_station desc
 Limit 3
 ```
| usertype | to_station_name | No_end_station
|---|---|---
Subscriber | Canal St & Adams St | 18870
Subscriber | Clinton St & Washington Blvd | 18188
Subscriber | Clinton St & Madison St | 16520

```sql
SELECT usertype, to_station_name, Count(to_station_name) AS No_end_station
FROM 
 (SELECT usertype, to_station_name
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT usertype, to_station_name
 FROM my-project-1-362921.cycalistic.2019q4) AS subtable
 WHERE usertype = 'Customer'
 GROUP BY usertype,to_station_name
 ORDER BY No_end_station desc
 Limit 3
 ```
| usertype | to_station_name | No_end_station
|---|---|---
Customer | Streeter Dr & Grand Ave | 9000
Customer | Lake Shore Dr & Monroe St | 4346
Customer | Millennium Park | 4095

4. Which day of the week the member and casual user use most the service.

```sql
SELECT usertype, Count (Day_of_week) AS No_use,
CASE 
    WHEN Day_of_week = 1 THEN 'Sunday'
    WHEN Day_of_week = 2 THEN 'Monday'
    WHEN Day_of_week = 3 THEN 'Tuesday'
    WHEN Day_of_week = 4 THEN 'Wednesday'
    WHEN Day_of_week = 5 THEN 'Thursday'
    WHEN Day_of_week = 6 THEN 'Friday'
    ELSE 'Saturday'
END AS Atual_day_of_week
FROM
( SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype
FROM my-project-1-362921.cycalistic.2019q1 ) AS subtable
WHERE usertype = 'Subscriber'
GROUP BY Day_of_week, usertype
ORDER BY No_use desc
LIMIT 3
```
| usertype | No_use | Atual_day_of_week
|---|---|---
Subscriber | 63983 | Thursday
Subscriber | 59672 | Friday
Subscriber | 58277 | Tuesday

```sql
SELECT usertype, Count (Day_of_week) AS No_use,
CASE 
    WHEN Day_of_week = 1 THEN 'Sunday'
    WHEN Day_of_week = 2 THEN 'Monday'
    WHEN Day_of_week = 3 THEN 'Tuesday'
    WHEN Day_of_week = 4 THEN 'Wednesday'
    WHEN Day_of_week = 5 THEN 'Thursday'
    WHEN Day_of_week = 6 THEN 'Friday'
    ELSE 'Saturday'
END AS Atual_day_of_week
FROM
( SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype
FROM my-project-1-362921.cycalistic.2019q1 ) AS subtable
WHERE usertype = 'Customer'
GROUP BY Day_of_week, usertype
ORDER BY No_use desc
LIMIT 3
```
| usertype | No_use | Atual_day_of_week
|---|---|---
Customer | 5993 | Saturday
Customer | 3766 | Sunday
Customer | 3375 | Friday

5. For the most common start station, which day of week has the most of use.
```sql
WITH temp1 AS (
    SELECT usertype, from_station_name, Count (Day_of_week) AS No_use,
CASE 
    WHEN Day_of_week = 1 THEN 'Sunday'
    WHEN Day_of_week = 2 THEN 'Monday'
    WHEN Day_of_week = 3 THEN 'Tuesday'
    WHEN Day_of_week = 4 THEN 'Wednesday'
    WHEN Day_of_week = 5 THEN 'Thursday'
    WHEN Day_of_week = 6 THEN 'Friday'
    ELSE 'Saturday'
END AS Atual_day_of_week
FROM
( SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q4) AS dayofweek
WHERE usertype = 'Subscriber'AND from_station_name = 'Canal St & Adams St'
GROUP BY Day_of_week, usertype, from_station_name
ORDER BY No_use desc
LIMIT  1
),
temp2 AS (
    SELECT usertype, from_station_name, Count (Day_of_week) AS No_use,
CASE 
    WHEN Day_of_week = 1 THEN 'Sunday'
    WHEN Day_of_week = 2 THEN 'Monday'
    WHEN Day_of_week = 3 THEN 'Tuesday'
    WHEN Day_of_week = 4 THEN 'Wednesday'
    WHEN Day_of_week = 5 THEN 'Thursday'
    WHEN Day_of_week = 6 THEN 'Friday'
    ELSE 'Saturday'
END AS Atual_day_of_week
FROM
( SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q4) AS dayofweek
WHERE usertype = 'Subscriber'AND from_station_name = 'Clinton St & Washington Blvd'
GROUP BY Day_of_week, usertype, from_station_name
ORDER BY No_use desc
LIMIT 1
),
temp3 AS (
SELECT usertype, from_station_name, Count (Day_of_week) AS No_use,
CASE 
    WHEN Day_of_week = 1 THEN 'Sunday'
    WHEN Day_of_week = 2 THEN 'Monday'
    WHEN Day_of_week = 3 THEN 'Tuesday'
    WHEN Day_of_week = 4 THEN 'Wednesday'
    WHEN Day_of_week = 5 THEN 'Thursday'
    WHEN Day_of_week = 6 THEN 'Friday'
    ELSE 'Saturday'
END AS Atual_day_of_week
FROM
( SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q4) AS dayofweek
WHERE usertype = 'Subscriber'AND from_station_name = 'Clinton St & Madison St'
GROUP BY Day_of_week, usertype, from_station_name
ORDER BY No_use desc
LIMIT 1 
)
SELECT *
FROM temp1 FULL JOIN temp2 ON temp1.usertype = temp2.usertype
JOIN temp3 ON temp1.usertype = temp3.usertype
```
| usertype | from_station_name  | No_use | Atual_day_of_week | usertype | from_station_name  | No_use | Atual_day_of_week | usertype | from_station_name  | No_use | Atual_day_of_week
|---|---|---|---|---|---|---|---|---|---|---|---
Subscriber | Canal St & Adams St| 3981 | Tuesday | Subscriber | Clinton St & Washington Blvd | 3653 | Tuesday | Subscriber | Clinton St & Madison St | 3355 | Tuesday

```sql
WITH 
temp1 AS (
SELECT usertype, from_station_name, Count (Day_of_week) AS No_use,
CASE 
    WHEN Day_of_week = 1 THEN 'Sunday'
    WHEN Day_of_week = 2 THEN 'Monday'
    WHEN Day_of_week = 3 THEN 'Tuesday'
    WHEN Day_of_week = 4 THEN 'Wednesday'
    WHEN Day_of_week = 5 THEN 'Thursday'
    WHEN Day_of_week = 6 THEN 'Friday'
    ELSE 'Saturday'
END AS Atual_day_of_week
FROM
( SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q4) AS dayofweek
WHERE usertype = 'Customer' AND from_station_name = 'Streeter Dr & Grand Ave'
GROUP BY Day_of_week, usertype, from_station_name
ORDER BY No_use desc
LIMIT 1),
temp2 AS (
  SELECT usertype, from_station_name, Count (Day_of_week) AS No_use,
CASE 
    WHEN Day_of_week = 1 THEN 'Sunday'
    WHEN Day_of_week = 2 THEN 'Monday'
    WHEN Day_of_week = 3 THEN 'Tuesday'
    WHEN Day_of_week = 4 THEN 'Wednesday'
    WHEN Day_of_week = 5 THEN 'Thursday'
    WHEN Day_of_week = 6 THEN 'Friday'
    ELSE 'Saturday'
END AS Atual_day_of_week
FROM
( SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q4) AS dayofweek
WHERE usertype = 'Customer' AND from_station_name = 'Lake Shore Dr & Monroe St'
GROUP BY Day_of_week, usertype, from_station_name
ORDER BY No_use desc
LIMIT 1
),
temp3 AS (
SELECT usertype, from_station_name, Count (Day_of_week) AS No_use,
CASE 
    WHEN Day_of_week = 1 THEN 'Sunday'
    WHEN Day_of_week = 2 THEN 'Monday'
    WHEN Day_of_week = 3 THEN 'Tuesday'
    WHEN Day_of_week = 4 THEN 'Wednesday'
    WHEN Day_of_week = 5 THEN 'Thursday'
    WHEN Day_of_week = 6 THEN 'Friday'
    ELSE 'Saturday'
END AS Atual_day_of_week
FROM
( SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q1
 UNION ALL
 SELECT CAST(start_time AS DATE) AS Start_date, EXTRACT(DAYOFWEEK FROM start_time) AS Day_of_week, usertype, from_station_name
 FROM my-project-1-362921.cycalistic.2019q4) AS dayofweek
WHERE usertype = 'Customer' AND from_station_name = 'Shedd Aquarium'
GROUP BY Day_of_week, usertype, from_station_name
ORDER BY No_use desc
LIMIT 1)
SELECT *
FROM temp1 JOIN temp2 ON temp1.usertype = temp2.usertype
JOIN temp3 ON temp1.usertype = temp3.usertype
```
| usertype | from_station_name  | No_use | Atual_day_of_week | usertype | from_station_name  | No_use | Atual_day_of_week | usertype | from_station_name  | No_use | Atual_day_of_week
|---|---|---|---|---|---|---|---|---|---|---|---
Customer | Streeter Dr & Grand Ave | 1616 | Saturday | Customer | Lake Shore Dr & Monroe St | 1495 | Saturday | Customer | Shedd Aquarium | 953 | Sunday

## Conclusion
1. The subscribers use the Cyclistic for short distance trip, and use it more frequently, while non subsciber use Cyclistc less but more a longer journey.
2. The most common start and end stations for subscriber are Canal St & Adams St, Clinton St & Washington Blvd	Clinton St & Madison St, and they usally use Cyclistic during the week, we can say that most of the subscribers use Cyclistic as a commute method for work. 
3. The two most common start and end stations for subscriber are Streeter Dr & Grand Ave, Lake Shore Dr & Monroe St, the thid most common start station is Shedd Aquarium, and end station is Millennium Park. Most of the normal customers use Cyclistic during the weekend. We can conclude that non subscirber use Cyclistic for leisure purpose. 
4. In order to attract new subscriber, the marketing team needs to forcus on staion Canal St & Adams St, Clinton St & Washington Blvd	Clinton St & Madison St, especially on Tuesday.
5. If want so convert already exist customer to subscriber, the marketing stratage can forcus on station Streeter Dr & Grand Ave, Lake Shore Dr & Monroe St on Saturday and Shedd Aquarium station on Sunday.
