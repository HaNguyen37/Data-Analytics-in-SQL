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
			
			Row	 null_gender	         null_birthyear
			1	 0.14646472451087497	 0.14110719485719059


			
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
		
		-- Calculate number of rides by Customer riders' gender
		
		SELECT gender, count(*) as Number_of_Customers
		From `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		WHERE usertype = "Customer"
		Group By gender;
		
		Row	 gender	    Number_of_Customers
		1	 unknown    536450
		2	 Male	    212742
		3	 Female	    131439
		
		-- Calculate the average birthyear for Subcriber and Customer riders
		
		SELECT AVG(birthyear) AS Birth_Year, usertype
		FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		GROUP BY usertype;
		
		Row	 Birth_Year	         usertype
		1	 1983.6026794685804	 Subscriber
		2	 1988.0449491120194	 Customer
		
		-- Calculate top 5 birthyear for Subscribers
		
		SELECT birthyear, Count(*) as Subscriber_rides,
		From `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		WHERE usertype = "Subscriber"
		Group By birthyear
		ORDER BY Subscriber_rides DESC
		LIMIT 5;
		
		Row	birthyear	Subscriber_rides
		1	 1992	        182199
		2	 1989	        171254
		3	 1990	        165733
		4	 1993	        164432
		5	 1991	        162024
		
		
		-- Calculate top 5 birthyear for Customer
		
		SELECT birthyear, Count(*) as Customer_rides,
		From `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		WHERE usertype = "Customer"
		Group By birthyear
		ORDER BY Customer_rides DESC
		LIMIT 6;
		
		Row	birthyear	Customer_rides
		1	 null	        532415
		2	 1994	        24815
		3	 1995	        24623
		4	 1993	        23363
		5	 1992	        21462
		6	 1989	        20707
		
		
		-- Calculate the mean of ride_length
		
		SELECT AVG(ride_length) AS MeanTime
		FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`;
		
		Row	 MeanTime
		1	 1450.465627603676
		
		
		-- Calculate the mean of ride_length for Subscriber and Customer riders
		
		SELECT AVG(ride_length) AS MeanTime, usertype
		FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		GROUP BY usertype;
		
		Row	 MeanTime	         usertype
		1	 859.66807064849331	 Subscriber
		2	 3421.0811429531864	 Customer
		
		
		-- Calculate max ride_length
		
		SELECT MAX(ride_length) as Max_ride_length, usertype
		FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		GROUP BY usertype;
		
		Row	Max_ride_length	   usertype
		1	9056634	           Subscriber
		2	10632022	   Customer
		
		
		-- Calculate number of rides by day of week
		SELECT EXTRACT(DAYOFWEEK from start_time) as day_of_week, Count(*) as Number_of_rides
		FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		GROUP BY day_of_week
		ORDER BY day_of_week;
		
		Row  day_of_week	Number_of_rides
		1	 1	        426407
		2	 2	        560269
		3	 3	        585680
		4	 4	        584022
		5	 5	        588287
		6	 6	        578107
		7	 7	        495219
		
		Note: 1 = 1 Sunday, 2 = Monday, 3 = 3 Tuesday, 4 = Wednesday, 5 = Thursday, 6 = Friday, 7 = Saturday
		
		-- Calculate number of rides for all users by day_of_week
		
		SELECT EXTRACT(DAYOFWEEK from start_time) as day_of_week, Count(*) as Number_of_rides_for_subscriber
		FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		WHERE usertype = "Subscriber"
		GROUP BY day_of_week
		ORDER BY day_of_week;
		
		-- -- Calculate number of rides for Subscriber and Customer riders by day_of_week
		
		SELECT 
		a.day_of_week,
		a.Number_of_rides_for_subscriber,
		b.Number_of_rides_for_customer
		FROM 
		    (
		    SELECT EXTRACT(DAYOFWEEK from start_time) as day_of_week, Count(*) as Number_of_rides_for_subscriber
		    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		    WHERE usertype = "Subscriber"
		    GROUP BY day_of_week) AS a
		LEFT JOIN
		    (
		    SELECT EXTRACT(DAYOFWEEK from start_time) as day_of_week, Count(*) as Number_of_rides_for_customer
		    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		    WHERE usertype = "Customer"
		    GROUP BY day_of_week) AS b
		ON a.day_of_week = b.day_of_week
		ORDER BY day_of_week;
		
		Row day_of_week	Number_of_rides_for_subscriber	Number_of_rides_for_customer
		1	 1	 256234	                        170173
		2	 2	 458780	                        101489
		3	 3	 497025	                        88655
		4	 4	 494277	                        89745
		5	 5	 486915	                        101372
		6	 6	 456966	                        121141
		7	 7	 287163	                        208056
		
	
		-- Calculate the average ride_length for users by day_of_week
		
		SELECT 
		a.day_of_week,
		a.Average_ridelength_for_subscriber,
		b.Average_ridelength_for_customer
		FROM 
		    (
		    SELECT EXTRACT(DAYOFWEEK from start_time) as day_of_week, AVG(ride_length) as Average_ridelength_for_subscriber
		    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		    WHERE usertype = "Subscriber"
		    GROUP BY day_of_week) AS a
		LEFT JOIN
		    (
		    SELECT EXTRACT(DAYOFWEEK from start_time) as day_of_week, AVG(ride_length) as Average_ridelength_for_customer
		    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		    WHERE usertype = "Customer"
		    GROUP BY day_of_week) AS b
		ON a.day_of_week = b.day_of_week
		ORDER BY day_of_week;
		
		Row	day_of_week  Average_ridelength_for_subscriber  Average_ridelength_for_customer
		1	 1	     924.17411038347973	                3371.1116922190949
		2	 2	     854.9570316927485	                3269.9933588861895
		3	 3	     849.15551934008079	                3444.7970334442443
		4	 4	     828.59068498027693	                3620.0439801660259
		5	 5	     826.78751116724345	                3597.0673558773542
		6	 6	     833.848656136343	                3610.5366391230209
		7	 7	     978.16230503234044	                3243.6666234090994
		
		
		-- Calculate number of rides by month
		
		SELECT 
		    EXTRACT(MONTH FROM start_time) as Month, 
		    COUNT(*) as Number_of_rides
		FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		GROUP BY Month
		ORDER BY Month;
		
		Row	Month	Number_of_rides
		1	 1	 103272
		2	 2	 96186
		3	 3	 165611
		4	 4	 265310
		5	 5	 367458
		6	 6	 475395
		7	 7	 557315
		8	 8	 590184
		9	 9	 493219
		10	 10	 371786
		11	 11	 177163
		12	 12	 155092
		
		
		-- Calculate number of rides for Subscriber and Customer riders by month
		
		SELECT 
		a.Month,
		a.Number_of_rides_for_subscriber,
		b.Number_of_rides_for_customer
		FROM 
		    (
		    SELECT EXTRACT(MONTH from start_time) as Month, Count(*) as Number_of_rides_for_subscriber
		    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		    WHERE usertype = "Subscriber"
		    GROUP BY Month) AS a
		LEFT JOIN
		    (
		    SELECT EXTRACT(MONTH from start_time) as Month, Count(*) as Number_of_rides_for_customer
		    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		    WHERE usertype = "Customer"
		    GROUP BY month) AS b
		ON a.month = b.month
		ORDER BY month;
		
		Row	Month	Number_of_rides_for_subscriber	Number_of_rides_for_customer
		1	 1	 98670	 4602
		2	 2	 93548	 2638
		3	 3	 149688	 15923
		4	 4	 217566	 47744
		5	 5	 285834	 81624
		6	 6	 345177	 130218
		7	 7	 381683	 175632
		8	 8	 403295	 186889
		9	 9	 364046	 129173
		10	 10	 300751	 71035
		11	 11	 158440	 18723
		12	 12	 138662	 16430
		
		
		-- Calculate number of rides by time of the day
		
		SELECT 
		    EXTRACT(HOUR FROM start_time) as Hour, 
		    COUNT(*) as Number_of_rides
		FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		GROUP BY Hour
		ORDER BY Hour;
		
		Row	Hour	Number_of_rides
		1	 0	 24078
		2	 1	 14404
		3	 2	 8664
		4	 3	 5611
		5	 4	 7789
		6	 5	 35770
		7	 6	 108232
		8	 7	 237708
		9	 8	 305780
		10	 9	 163833
		11	 10	 145464
		12	 11	 179966
		13	 12	 206267
		14	 13	 207125
		15	 14	 206385
		16	 15	 243440
		17	 16	 375778
		18	 17	 475154
		19	 18	 315253
		20	 19	 208760
		21	 20	 134160
		22	 21	 96193
		23	 22	 69701
		24	 23	 42476
		
		
		-- -- Calculate number of rides for Subscriber and Customer riders by time of the day
		
		SELECT 
		a.Hour,
		a.Number_of_rides_for_subscriber,
		b.Number_of_rides_for_customer
		FROM 
		    (
		    SELECT EXTRACT(HOUR from start_time) as Hour, Count(*) as Number_of_rides_for_subscriber
		    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		    WHERE usertype = "Subscriber"
		    GROUP BY Hour) AS a
		LEFT JOIN
		    (
		    SELECT EXTRACT(HOUR from start_time) as Hour, Count(*) as Number_of_rides_for_customer
		    FROM `portfolio-346202.2019_divvy_tripdata.2019_tripdata_cleaned`
		    WHERE usertype = "Customer"
		    GROUP BY Hour) AS b
		ON a.Hour = b.Hour
		ORDER BY Hour;
		
		Row	Hour	Number_of_rides_for_subscriber	Number_of_rides_for_customer
		1	 0	 15874	                        8204
		2	 1	 9026	                        5378
		3	 2	 5329	                        3335
		4	 3	 3686	                        1925
		5	 4	 6614	                        1175
		6	 5	 33156	                        2614
		7	 6	 102141	                        6091
		8	 7	 224842	                        12866
		9	 8	 283982	                        21798
		10	 9	 135131	                        28702
		11	 10	 100700	                        44764
		12	 11	 119930	                        60036
		13	 12	 136676	                        69591
		14	 13	 131946	                        75179
		15	 14	 127914	                        78471
		16	 15	 163346	                        80094
		17	 16	 292901	                        82877
		18	 17	 390667	                        84487
		19	 18	 247234	                        68019
		20	 19	 158385	                        50375
		21	 20	 99757	                        34403
		22	 21	 71268	                        24925
		23	 22	 48535	                        21166
		24	 23	 28320	                        14156
		

**Summary of my analysis: Differences between Subscriber and Customer**

Note:
Subscriber: People who purchase annual membership from Cyclistic
Customer: People who purchase single-ride or full-day passes from Cyclistic

In 2019,  the number of rides taken by Subscribers (or annual members) is 2,937,360 rides, accounts for about 77% of the total rides.
	
While the average ride duration of all riders is 24 minutes, this time for Subscriber is 14 minutes and Customer is 57 minutes. So the average ride length for a Customer is greater than a Subscriber by 43 minutes.
	
Subscribers take trips more frequently on weekday with over 450,000 rides per day on weekday compare to under 290,000 rides per day on Saturday and Sunday. Whereas, Customers usually take trips during weekend with over 170,000 rides per day on Saturday and Sunday, compare to under 120,000 rides on weekday.
	
As Subscribers take trip more frequent on weekday, the peak hours for Subscribers are between 7am and 9am, and between 4pm and 6pm.
	
Summer time in Chicago from June to August has the highest number of rides in both Subscriber and Customer.
	
Male riders account significantly than Female riders in both Subscriber and Customer segment.
	
The most popular birthyears for both Subscriber and Customer are quite similar, from 1989 to 1992.
	

**Data Visualisation**

Create data visualisation to better share the findings.

Please click here to see the interactive dashboard in Tableau Public,

Or This ULR: https://public.tableau.com/authoring/TheCyclisticbike-shareanalysis/Dashboard1#1

 ![image](https://user-images.githubusercontent.com/103098013/161947093-8268b901-76b0-4105-87ff-c12bea22f7fa.png)


**Recommendations**
		
To design marketing strategies aimed at converting casual riders (or Customers) who purchase single ride or full-day passes into members (or Subscribers) who purchase annual memberships from Cyclistic, there are three main recommendations from the findings of the data analysis stage:
		 
**Targeting the young demographic range between 25 and 35 on social media platforms**
The riders with the birth year between 1989 and 1995 contributed significantly to the number of rides for both casual riders and members. Therefore, creating marketing campaigns on different social media platforms that focus on young riders has more potential to convert them into members.
		 
**Target female customers**
In the Subscriber segment, the number of Male riders is three times higher than Female riders, while this ratio in the Customer segment is 1.5 Male riders vs. 1 Female rider. These numbers show that the are lots of interest from female customers but the conversion rate to Subscribers seems low.
		 
**Increasing promotion campaigns in Summer**
Summertime in Chicago from June to August is the favourite time for riders in both Subscribers and Customers. Creating marketing campaigns that are suitable with time and seasons for riders would convert casual riders to members of Cyclistic.















