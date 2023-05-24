# Which membership tier will be more profitable?

### To view complete version of the notebook, [click here](https://www.kaggle.com/code/vishalpatel1266/case-study-1)

## About the company
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. However, Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. Although the pricing flexibility helps Cyclistic attract more customers, Moreno believes that maximizing the number of annual members will be key to future growth.

Business has set a clear goal: Design marketing strategies aimed at converting casual riders into annual members. In order to do that, however, the marketing analyst team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. Moreno and her team are interested in analyzing the Cyclistic historical bike trip data to identify trends.


## Buisness Tasks:
- How do annual members and casual riders use Cyclistic bikes differently?Why would casual riders buy Cyclistic annual memberships?
- How can Cyclistic use digital media to influence casual riders to become members?
- Why would casual riders buy Cyclistic annual memberships?


### About the Dataset:
- The dataset consists of 12 files which conisits ride history data of each month
- The data has been made available by Motivate International Inc. under this license
- The Divvy system data is owned by the City (“Data”) available to the public, thus it is trustable and has integrity
- Data has been improved every time a data analyst makes recommendations after using it to run analysis


### Errors and Inconsistencies in the Data
- Station Name and id columns have a lot of null values which accounts for almost 15% of total data
- Trip end lattitude and longitude data is also missing in 5953 roows which is very negligible as it accounts for 0.1% of total data
- Lattitude and longitude data is unnecessary and will be removed from dataset
- Station name "HQ QR" needs to be removed as it is for QA testing


### Data Cleaning
- New columns will be added foe better analysis
    - Extracting date column from started_at
    - Extracting day, month and year from the date column
- Adding Day of the week name to identify weekdays vs weekend trend
- Checking the range of data and find out if they are not within the selcted 12 month range
- Calculate ride_length to identify trends in how long riders use the bikes


### Data Analysis Stage:
- ride_length attribute will be a main focus for this analysis
- We will aggregate the data to find out mean, max and min of ride_length by member type (Casual or Member)
- We will identify how many times bike services are used by month or by day of the week
- We will also identify the nature of the ride_length based on month, day of the week and membership type


