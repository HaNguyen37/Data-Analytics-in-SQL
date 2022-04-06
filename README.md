# Data-Analytics-in-SQL
Google Data Analytics Professional Capstone Project: The Cyclistic Bike-Share Analysis
---
**Overview**

This Case Study is a Capstone project for the Google Data Analytics Course by Coursera. For the purpose of this case study, I am the junior data analyst working in the marketing analyst team at Cyclistic, a bike-share company in Chicago.

In 2016,Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.

Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as Customer. People who purchase annual memberships are Subscriber

The goal of this project is to understand how Customer and Subscriber use Cyclistic bikes differently  by analyzing the Cyclistic historical bike trip data to identify trends. This insight will help the marketing team of Cyclistic to design a new marketing strategy to convert casual riders or Customer into annual members or Subscriber which will impact the profits of the company over time.

**Prepare Data for Exploration**

The dataset being used is collected by Cyclistic and is stored in their company database. It contains Cyclistic’s historical trip data that happened from January 2019 to December 2019. The data is separated by quarter, with detailed information.

For the purposes of this case study, the datasets are appropriate and will enable you to answer the business questions. The data has been made available by Motivate International Inc. under Data License Agreement | Divvy Bikes. 

The dataset for analysis is stored locally for the purpose of this project. The data contains information about all the rides and didn’t find any bias with it. As I’ll be using the first-party data, it’s original, reliable, and cited. Also reviewed the data thoroughly to make sure that there’s no personally identifiable information.

**Processing Data**

Tools used: Spreadsheet and SQL
Data processing steps:
	- Use spreadsheet to get familiar with dataset, check columns in each spreadsheet for consistence.
	- Combine dataset of four separate quarterly data files into one single worksheet of the year 2019 by using SQL
	
			
			SELECT *
			 FROM `portfolio-346202.2019_divvy_tripdata.2019Q1_tripdata` 
			 union all
			 select *
			 From `portfolio-346202.2019_divvy_tripdata.2019Q2_tripdata` 
			 union all
			 select *
			 from `portfolio-346202.2019_divvy_tripdata.2019Q3_tripdata`
			 union all
			 select *
			 from `portfolio-346202.2019_divvy_tripdata.2019Q4_tripdata` 
			 order by start_time;
			 
	- Verify that number of rows in the combined table equal the total number of rows from four separate tables.
	- Check and remove duplicate values.
	
			    SELECT trip_id, COUNT(*)
			    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata` 
			    GROUP BY trip_id
			    HAVING COUNT(*) >1;
	
	- Create a column called “ride_length.” Calculating the length of each ride by subtracting the column “started_at” from the column “ended_at” . Deleted rows with ride_length value < 0
			
			
			    SELECT
			        *,
			        TIMESTAMP_DIFF(end_time,start_time,second) as ride_length
			    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata`;
			
	- Delete rows with ride_length < 0 (There are 13 rows have ride_length value < 0)
	
			    DELETE FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
			    WHERE ride_length < 0;
			    
	- Check Null Values in columns: trip_id, start_time, end_time, usertype. And trim data in all columns with data type of string to remove unintended whitespaces
			
			    SELECT *
			    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
			    WHERE trip_id IS NULL;
			    
			    SELECT *
			    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
			    WHERE start_time IS NULL;
			
			    SELECT *
			    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
			    WHERE end_time IS NULL;
			
			    SELECT *
			    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
			    WHERE usertype IS NULL;
			
	- Trim data
	
			    SELECT
			        TRIM(from_station_name),
			        TRIM(to_station_name), TRIM(usertype), TRIM(gender), 
			    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`;
			    
	- Count NULL Values in columns gender and birthyear.
	
	
			SELECT 
			 COUNTIF(gender is NULL)/COUNT(1) as null_gender,
			 COUNTIF (birthyear is NULL)/COUNT(1) as null_birthyear
			FROM `porfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`;
			
![image](https://user-images.githubusercontent.com/103098013/161942673-8970bbd4-54cb-4751-9254-a75f82ee11bd.png)
			
(Note: As Null Values account for about 14% in columns of gender and birthyear, the demographic analysis results might bias)

**Analysing Data**

Now the data is stored appropriately and has been prepared for analysis. Next step is analysing the Cyclistic historical bike trip data to identify trends.

Calculations: 
	- Number of rides by users in 2019 
	- The mean of ride length, The average ride length by riders
	- Number of rides for all user types by hour, by day of week, and by month
	
		-- Calculate number of riders in 2019
		
		SELECT usertype, count(*) as Number_of_rides
		FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		GROUP BY usertype;
		
		Row	usertype	Number_of_rides
		1	 Subscriber	 2937360
		2	 Customer	 880631

		-- Calculate number of rides by Subscriber riders' gender
		
		SELECT gender, count(*) as Number_of_Subscribers
		From `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		WHERE usertype = "Subscriber"
		Group By gender;
		
		Row	gender	Number_of_Subscribers
		1	unknown	 22751
		2	Male	 2188071
		3	Female	 726538









