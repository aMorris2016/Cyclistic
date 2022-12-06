# Cyclistic
Google Data Analytics Professional Certificate Capstone Project

## Introduction
This is an exploratory data analysis case study about Cyclistic, a fictional bike-share company based in Chicago. Working as a junior data analyst with the marketing analyst team, I am given the task to provide insights about how casual riders and annual members use Cyclistic bikes differently, and then provide recommendations to convert casual riders into annual members.

To answer the key business questions, I will follow the steps of the data analysis process: ask, prepare, process, analyze, share and act.

## About the Company
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago

Cyclistic offers flexible pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members. 

Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. Lily Moreno, the director marketing, advocates that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, she believes there is a very good chance to convert casual riders into members. 

## Ask
Project Goal: Design marketing strategies aimed at converting casual riders into annual members. 

There are three questions that will guide the future of the marketing program:
1.	How do annual members and casual riders use Cyclistic bikes differently? 
2.	Why would casual riders buy Cyclistic annual memberships? 
3.	How can Cyclistic use digital media to influence casual riders to become members? 

>**Business Task:** Analyze Cyclistic’s historical trip data for the year 2020 to identify trends and understand how annual members and casual riders use Cyclistic bikes differently.

## Prepare
### Data Source
Cyclistic provided historical data sets collected from sensors on the docking stations. For the purposes of this study, analysis will be based on data for the period January 2021 to December 2021. 

Note: While Cyclistic is a fictional company, [data set](https://divvy-tripdata.s3.amazonaws.com/index.html) is from Divvy – Chicago’s bike share service. The data has been made available by Motivate International Inc. under this [license](https://ride.divvybikes.com/data-license-agreement). This is public data, but no personally identifiable information is included. There is also no information provided regarding costs and pricing structure.

Initial inspection of the data set shows 12 .csv files, one for each month of the year, with the following columns and data types:
```
ride_id: STRING
rideable_type: STRING
started_at, TIMESTAMP
ended_at: TIMESTAMP
start_station_name: STRING
start_station_id: STRING
end_station_name: STRING
end_station_id: STRING
start_lat: FLOAT
start_lng: FLOAT
end_lat: FLOAT
end_lng: FLOAT
member_casual: STRING
```

## Process
### Data Cleaning and Transformation
In this phase, 12 .csv files representing each month of 2021 were downloaded locally and manipulated using Excel to filter, sort and remove unnecessary data accordingly:

- Checked and removed duplicates
- Added `ride_month` column data type "string”. Eg. January for all rides in file 202101_tripdata.csv
- Changed format of `started_at` and `ended_at columns` to yyyy-mm-dd h:mm:ss
  Format cells > Custom > yyyy-mm-dd h:mm:ss
- Extracted date and times from `started_at` and `ended_at` to create the following columns:
  1.  `start_date`	=INT(started_at)  
  2.  `start_time`	=started_at – start_date
  3.  `end_date`	=INT(ended_at)
  4.  `end_time`	=ended_at – end_date

 The resulting dates and times should be formatted as follows:
 1. `start_date` and `end_date` 	Format cells > Custom > yyyy-mm-dd
 2. `start_time` and `end_time`	Format cells > Time > 37:30:55
 - Added column `day_of_week` =TEXT(start_date, “dddd”)
 - Corrected formatting of some incorrect “ride_id” values
 
![incorrect ride_id values](https://github.com/aMorris2016/Cyclistic/blob/main/incorrect_ride_id.png)

- Deleted rows where the `start_station` / `end_station` values were suffixed with “Bike-checking” or “Test”. It was assumed that these rides are test rides done by the company and not completed by customers, thus do not represent real user’s experience.

![test_rides](https://github.com/aMorris2016/Cyclistic/blob/main/test_rides.png)

To aggregate all the .csv files into one data set and perform further data cleaning, the pre-cleaned files were uploaded to BigQuery.

- Merged 12 tables to create one data set.
```
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202101_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202102_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202103_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202104_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202105_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202106_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202107_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202108_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202109_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202110_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202111_tripdata` 
UNION ALL
SELECT * FROM `case-study-cyclistic-365423.cyclistic_2021.202112_tripdata`
```
- Added column `ride_duration` to show length of each ride in minutes.
```
SELECT 
  ride_id,
  timestamp_diff(ended_at, started_at, minute) AS ride_duration 
FROM `case-study-cyclistic-365423.cyclistic_2021.2021_tripdata`
```
- Created a new table filtering out ride trips with invalid duration, i.e., trips with 0 or negative ride_duration values (63,315 rows).
```
CREATE TABLE cyclistic_2021.cyclistic_2021_tripdata_cleaned
AS
  SELECT *
  FROM `case-study-cyclistic-365423.cyclistic_2021.2021_tripdata` 
  WHERE ride_duration > 0
```
- Cleaned data and merged data set “cyclistic_2021_tripdata_cleaned” has a total of 5,530,872 rows

## Analyze and Share
BigQuery, SQL and Tableau were used to derive insights on how Cyclistic casual users and and members ride differently. 

### Total ride trips
```
SELECT  
  member_casual AS customer_type,
  COUNT(ride_id) AS num_rides
FROM `case-study-cyclistic-365423.cyclistic_2021.cyclistic_2021_tripdata_cleaned`
GROUP BY member_casual
```
![total rides by customer type](https://github.com/aMorris2016/Cyclistic/blob/main/totalRides_by_customerType.png)
> Cyclistic members ride more often than casual riders.

### Ride trips per month
```
SELECT 
  member_casual AS customer_type,
  ride_month AS month,
  COUNT (ride_id) AS num_trips
FROM `case-study-cyclistic-365423.cyclistic_2021.cyclistic_2021_tripdata_cleaned`
GROUP BY customer_type, month
ORDER BY num_trips DESC;
```
![rides per month](https://github.com/aMorris2016/Cyclistic/blob/main/ridetrips_per_month.png)
> July and August were the best performing months for both members and casual riders. While members ride more often for the duration of 2021, usage by casual riders were higher during the summer months.

### Ride type used
```
SELECT 
  member_casual AS customer_type,
  rideable_type AS bike_type,
  COUNT(ride_id) AS num_ride_type
FROM `case-study-cyclistic-365423.cyclistic_2021.cyclistic_2021_tripdata_cleaned` 
GROUP BY customer_type, bike_type;
```
![rides by bike type](https://github.com/aMorris2016/Cyclistic/blob/main/rides_by_bike_type.png)
> Both members and casual riders favor classic bikes over electric bikes. Docked bikes were the least used.

### Average ride duration
```
SELECT  
  member_casual AS customer_type,
  AVG(ride_duration) AS Average_ride_duration
FROM `case-study-cyclistic-365423.cyclistic_2021.cyclistic_2021_tripdata_cleaned`
GROUP BY customer_type;
```
![ride duration by customer type](https://github.com/aMorris2016/Cyclistic/blob/main/ridetrip_duration.png)
> Casual riders ride significantly longer than members with an average duration of 31.49 minutes per trip.

### Average ride duration by month
```
SELECT 
  member_casual AS customer_type,
  ride_month AS month,
  AVG(ride_duration) AS average_ride_duration
FROM `case-study-cyclistic-365423.cyclistic_2021.cyclistic_2021_tripdata_cleaned` 
GROUP BY customer_type, ride_month
ORDER BY average_ride_duration DESC;
```


### Daily trips
```
SELECT 
  member_casual AS customer_type,
  day_of_week AS day,
  COUNT (ride_id) AS num_trips
FROM `case-study-cyclistic-365423.cyclistic_2021.cyclistic_2021_tripdata_cleaned`
GROUP BY customer_type, day
ORDER BY num_trips DESC;
```
> Saturdays and Sundays are the most popular days for casual riders. Members rode the most on Tuesdays and Wednesdays.

### Average ride duration by day of week
```
SELECT 
  member_casual AS customer_type,
  day_of_week AS day,
  AVG(ride_duration) AS average_ride_duration
FROM `case-study-cyclistic-365423.cyclistic_2021.cyclistic_2021_tripdata_cleaned` 
GROUP BY customer_type, day_of_week
ORDER BY average_ride_duration;
```
> Casual riders ride longer during weekends, while members ride longer on weekdays.




## Act


