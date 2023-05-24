---
title: "Case Study 1"
author: "Vishal Patel"
date: "`r Sys.Date()`"
output:
  pdf_document: default
  html_document: default
---

```{r setup, include=FALSE}
options(repos = c(CRAN = "http://cran.rstudio.com"))
```


## Which membership tier will be more profitable?

### About the company
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

## Processing Data:

### 1. Installing and loading the required libraries

```{r}
# Installing packages
install.packages("tidyverse")
install.packages("lubridate")
install.packages("dplyr")
install.packages("readr")
install.packages("ggplot2")
```

```{r}
#Loading packages
library(tidyverse)
library(lubridate)
library(ggplot2)
library(dplyr)
library(readr)
```

### 2. Loading all the data

```{r}
# Creating a list of filenames so that they can be loaded all at once
file_names <- c("202205-divvy-tripdata.csv", "202206-divvy-tripdata.csv", "202207-divvy-tripdata.csv", "202208-divvy-tripdata.csv", 
                "202209-divvy-tripdata.csv", "202210-divvy-tripdata.csv", "202211-divvy-tripdata.csv", "202212-divvy-tripdata.csv",
                "202301-divvy-tripdata.csv", "202302-divvy-tripdata.csv", "202303-divvy-tripdata.csv", "202304-divvy-tripdata.csv")
```

```{r}
#Setting working directory
setwd("/Users/MeowMac/Downloads/Case Study/")
```

```{r}
# Create an empty list to store the data frames for each month
fileNames_list <- list()

# Initialize the df_names vector
df_names <- character(length(file_names))

# Initialize the vector to store filenames if they have rows with missing values in every column
filenames_with_missing_rows <- character(0)
```

```{r}
# Loop through each file and load the .csv files

for (i in seq_along(file_names)) {
  
  # Read the CSV file and assign it to a data frame
  df_names[i] <- paste0("m", sprintf("%02d", 13 - i), "_", gsub("-", "_", substr(file_names[i], 3, 8)))
  fileNames_list[[df_names[i]]] <- read_csv(file_names[i])
  
  # Count the number of rows with missing values in all columns
  missing_rows_count <- sum(apply(is.na(fileNames_list[[df_names[i]]]), 1, all))
  
  # Print the result
  cat("Rows with missing values in all columns in", file_names[i], ":", missing_rows_count, "\n")
  
  # Check if missing_rows_count is greater than 0
  if (missing_rows_count > 0) {
    filenames_with_missing_rows <- c(filenames_with_missing_rows, file_names[i])
  }
}
```

### 3. Combining all data into one data frame

* Checking if column names are same in all files *
```{r}
# Checking if column names are same in all files before combining the data

for (i in seq_along(fileNames_list)) {
 
  # Get the dataframe name
  df_name <- names(fileNames_list)[i]
  
  # Print the dataframe name
  cat("Dataframe:", df_name, "\n")
  
  # Print the column names
  cat("Column Names:", colnames(fileNames_list[[df_name]]), "\n")
  
  # Check if column names match with the first dataframe
  if (i > 1) {
    if (!identical(colnames(fileNames_list[[df_name]]), colnames(fileNames_list[[1]]))) {
      cat("Column names do not match with the first dataframe.\n")
    } else{
      cat("\n \n Column names do match with the first dataframe.\n\n\n")
    }
  }
}
```

```{r}
# Check was successful, now combining all files into one

all_trips_last_year <- bind_rows(fileNames_list)
```


```{r}
# Calculate the number of null values in each column
null_counts <- colSums(is.na(all_trips_last_year))

# Calculate the percentage of null values in each column
null_percentages <- (null_counts / nrow(all_trips_last_year)) * 100

# Print the number and percentage of null values for each column
for (i in seq_along(null_counts)) {
  cat("Column:", names(null_counts)[i], "\n")
  cat("Number of null values:", null_counts[i], "\n")
  cat("Percentage of null values:", null_percentages[i], "%\n\n")
}
```


### Errors and Inconsistencies in the Data
Station Name and id columns have a lot of null values which accounts for almost 15% of total data
Trip end lattitude and longitude data is also missing in 5953 roows which is very negligible as it accounts for 0.1% of total data
Lattitude and longitude data is unnecessary and will be removed from dataset

## Data Cleaning


```{r}
#Checking the structure of dataset
str(all_trips_last_year)
```

### Data clean up plan:
- New columns will be added foe better analysis
    - Extracting date column from started_at
    - Extracting day, month and year from the date column
- Adding Day of the week name to identify weekdays vs weekend trend
- Checking the range of data and find out if they are not within the selcted 12 month range
- Calculate ride_length to identify trends in how long riders use the bikes



```{r}
#Adding new columns
all_trips_last_year$date <- as.Date(all_trips_last_year$started_at)
all_trips_last_year$month <- format(as.Date(all_trips_last_year$date), "%m")
all_trips_last_year$monthName <- month(as.Date(all_trips_last_year$date), label = TRUE, abbr = FALSE)
all_trips_last_year$day <- format(as.Date(all_trips_last_year$date), "%d")
all_trips_last_year$year <- format(as.Date(all_trips_last_year$date), "%Y")
all_trips_last_year$day_of_week <- format(as.Date(all_trips_last_year$date), "%A")
```

```{r}
# Check if the trip times are within the range
cat("Earliest Trip Start: ", as.character(min(all_trips_last_year$started_at)))
cat("\nLatest Trip Start: :",as.character(max(all_trips_last_year$started_at)))
cat("\n\nEarliest Trip End: ",as.character(min(all_trips_last_year$ended_at)))
cat("\nLatest Trip End: ",as.character(max(all_trips_last_year$ended_at)))
```

```{r}
# Add a "ride_length" calculation to all_trips (in seconds)
all_trips_last_year$ride_length <- difftime(all_trips_last_year$ended_at,all_trips_last_year$started_at)

# Convert "ride_length" to numeric so we can run calculations on the data
all_trips_last_year$ride_length <- as.numeric(as.character(all_trips_last_year$ride_length))
is.numeric(all_trips_last_year$ride_length)

#Checking the range of ride_length
print(min(all_trips_last_year$ride_length))
print(max(all_trips_last_year$ride_length))
```

```{r}
cat("Count of Negative values: ", sum(all_trips_last_year$ride_length < 0))
```

- It is found that ride_length values are negative, which should be zero or more
- Only 103 entires are found negative, which is very negligible


```{r}
# Columns with "HQ QR" values in start_station_name is for QA purposes which needs to be removed along wiht negative ride_length values
# Creating a new version of the dataframe (v2) since data is being removed
all_trips_last_year_v2 <- all_trips_last_year[!(all_trips_last_year$start_station_name == "HQ QR" | all_trips_last_year$ride_length < 0), ]
```

```{r}
# Count rows where every column has missing values
print(sum(rowSums(is.na(all_trips_last_year_v2)) == ncol(all_trips_last_year_v2)))
```

```{r}
# Remove rows with missing values in every column
all_trips_last_year_v2 <- all_trips_last_year_v2[!rowSums(is.na(all_trips_last_year_v2)) == ncol(all_trips_last_year_v2), ]

# Count rows where every column has missing values
print(sum(rowSums(is.na(all_trips_last_year_v2)) == ncol(all_trips_last_year_v2)))
```

## Data Analysis Stage:
- ride_length attribute will be a main focus for this analysis
- We will aggregate the data to find out mean, max and min of ride_length by member type (Casual or Member)
- We will identify how many times bike services are used by month or by day of the week
- We will also identify the nature of the ride_length based on month, day of the week and membership type



```{r}
#Summarizing data for ride_length columnm
summary(all_trips_last_year_v2$ride_length)
```

```{r}
# Compare members vs casual users for mean ride_length

# analyze ridership data by type and weekday
all_trips_last_year_v2 %>% 
  group_by(member_casual) %>%  #groups by usertype and weekday
  summarise(number_of_rides = n()							#calculates the number of rides and average duration 
            ,average_duration = mean(ride_length), total_duration = sum(ride_length)) %>% 		# calculates the average duration
  arrange(average_duration)
```

```{r}
# Compare members vs casual users for max ride_length

print(setNames(aggregate(all_trips_last_year_v2$ride_length ~ all_trips_last_year_v2$member_casual, FUN = max), 
               c("Member Type", "Max Ride Length")))
```

```{r}
# Compare members vs casual users for min ride_length

print(setNames(aggregate(all_trips_last_year_v2$ride_length ~ all_trips_last_year_v2$member_casual, FUN = min), 
               c("Member Type", "Min Ride Length")))
```



```{r}
# Create the plot
plot <- ggplot(all_trips_last_year_v2, aes(x = monthName, y = ride_length, fill = member_casual)) +
  geom_bar(stat = "summary", fun = "mean", position = "dodge") +
  labs(title = "Average Ride Length by Month",
       x = "",
       y = "Average Ride Duration (Seconds)",
       fill = "Member Type") +
  theme_minimal()

# Adjust the appearance of the plot
plot +
  theme(plot.title = element_text(size = 16, face = "bold"),
        axis.text.x = element_text(angle = 45, hjust = 1))
```



```{r}
# Let's visualize the number of rides by rider type
all_trips_last_year_v2 %>% 
  group_by(member_casual, monthName) %>% 
  summarise(number_of_rides = n()/1000, average_duration = mean(ride_length)) %>% 
  arrange(member_casual, monthName)  %>% 
  ggplot(aes(x = monthName, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge") + labs(title = "Number of Rides by Month",
       x = "",
       y = "Number of Rides (x1000)") + theme(plot.title = element_text(size = 16, face = "bold"),
        axis.text.x = element_text(angle = 45, hjust = 1))
```
```{r}
# Let's create a visualization for average duration
all_trips_last_year_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge") + labs(title = "Average Ride Duration per Day",
       x = "",
       y = "Average Ride Duration (Seconds)") + theme(plot.title = element_text(size = 16, face = "bold"),
        axis.text.x = element_text(angle = 45, hjust = 1))
```

