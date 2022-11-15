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

Initial inspection of the data set shows 12 .csv files, one for each month of the year, with the following columns and data type:
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
![incorrect ride_id values](





## Process

## Analyze

## Share

## Act


