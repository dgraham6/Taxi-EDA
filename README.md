# **Exploratory Data Analysis on Taxi Data**  
<center>

Drake Graham
<br>
dgraham7362@gmail.com  

</center>

## Introduction  

Did you know you spend around <span style="color:red">37,935</span> hours of your life driving? According to this [estimate](https://www.tempo.io/blog/how-do-people-spend-their-time#:~:text=According%20to%20a%20study%20done%20by%20the%20Harvard%20health), the average American spends a significant amount of their short life driving. 

Computer Science majors spend a lot of time trying to shave off milliseconds from their programs, so it follows that we might also want to spend the time to optimize such a time-consuming activity in all of our lives. If you're like me, you've always been curious about how software like Google Maps, Apple Maps, and Waze estimate ETAs and why there are differences between their given routes. 

In this project, I'll attempt to estimate travel times from a large dataset of completed taxi trips and compare that to other travel time estimators.

I'm using the [Taxi Trips in 2024 in the District of Columbia dataset](https://catalog.data.gov/dataset/taxi-trips-in-2024) from the Department of For-Hire Vehicles, which is intended for public access and use. As of today, there are 10 taxi files combined, totaling over 2 million rows. This data ranges from January 1st, 2024, to November 5th, and it is continuously updated until the year is over. The dataset includes 27 columns, with some of the most relevant for this project being the pickup and drop-off locations, trip duration, and mileage. 

Although I've focused on this specific dataset, my pipeline is adaptable to similar datasets, ensuring it is not limited to this single dataset.

## Map Visualization of Trip Origins  

The map below displays the origins of a sample of 1,000 taxi trips in Washington, D.C., using marker points to represent the geographic distribution.  

<iframe
  src="assets/map_with_markers.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## More Data  

Before diving deep into the taxi log data, we enhance our resources by retrieving hourly weather data using the [Open-Meteo API](https://open-meteo.com/) and performing a left merge with our taxi trip dataset. This additional weather data allows us to incorporate environmental factors, such as temperature, precipitation, and wind speed, that could impact travel times and driving conditions.  

|   precip(mm) |   snow(in) |
|-------------:|-----------:|
|        0.009 |          0 |
|        0.009 |          0 |
|        0.009 |          0 |
|        0.009 |          0 |
|        0.009 |          0 |

To further enrich our dataset, we use the [OSRM API](http://project-osrm.org/) to obtain predicted fastest routes and route distances for each trip. This provides a reference for evaluating how the actual taxi routes compare to the optimal routes in terms of distance and duration. By merging these external datasets, we aim to develop a more comprehensive analysis of the factors influencing trip durations and route optimizations.

|   duration(s) |   distance(m) |
|--------------:|--------------:|
|         102.1 |        1000.2 |
|         498.6 |        6198.7 |
|         295.9 |        3659.3 |
|         133.3 |        1647.7 |
|         208.7 |        2348.6 |

## Data Cleaning  

When examining the dataset, we can immediately identify certain columns as irrelevant to the problem we aim to address. For example:  

- `FAREAMOUNT`  
- `GRATUITYAMOUNT`  
- `PAYMENTTYPE`  

These columns are either completely unrelated or simply functions of the trip duration. To narrow our focus, these columns can be safely dropped.  

### Handling Missing Data  

A significant challenge in the dataset is the presence of numerous NaN values:  

1. **Coordinates of Origin and Destination**  
   - If either the origin or destination coordinates are missing, there is no reliable way to impute these values. Since these data points are crucial for estimation, any rows with missing coordinates must be dropped entirely.  

2. **`PROVIDERNAME`**  
   - This column is entirely NaN. While this information might be valuable in other datasets, its absence here leads us to drop the column altogether.  

3. **`AIRPORT`**  
   - Although the `AIRPORT` column contains many NaN values, it is a binary column. We can treat the NaN values as an indicator that the trip likely did not involve an airport.  
   - To validate this, we use the origin coordinates to calculate if the trip started near an airport. After applying this logic, we find that none of the trips in the dataset originated close to an airport, confirming our assumption to treat NaN values as "false."  

### Outliers and Extreme Values  

Another issue is the presence of extreme values in the `DURATION` and `DISTANCE` columns, likely caused by input errors.  

- While short trips (e.g., 3 minutes) are plausible, a trip lasting over three days or spanning more than 50 miles is not.  
- Given that Washington, D.C., is approximately 50 miles wide, these outliers are highly unrealistic and can be safely removed due to their rarity.  

By addressing these issues, we significantly improve the quality and reliability of the dataset, ensuring that it is ready for analysis.


## Trip Duration Analysis  

After removing the outliers, we can finally visualize what we aim to predict. By creating this graph, we can gain a better understanding of the distribution of trip durations in the dataset. Below is the resulting histogram:

<iframe
  src="duration_histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

By observing the distribution, we notice a significant number of trips with durations close to 0 seconds in our dataset. While very short taxi trips are possible, these are likely due to human errors, such as prematurely stopping and restarting trips. Apart from this anomaly, the distribution appears to be fairly symmetric and roughly normal, centered around 600 seconds (or 10 minutes). As the duration increases, there are a few scattered outliers, but the majority of the data fits within a consistent range.

## Average Trip Duration by Day of the Week  

Using the timestamp data and pandas' datetime features, we created a new column, `Day of the Week`, to categorize trips by the day they occurred. We then calculated the average trip duration (in minutes) for each day of the week.  

The chart below illustrates the variation in trip durations throughout the week. This analysis can provide insights into how trip lengths vary on weekdays versus weekends.  

<iframe
  src="trip_count_by_day.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
## Pivot Table Analysis  

To gain deeper insights into taxi trip behavior, we grouped the data by both the **Day of the Week** and the **Hour of Day**, examining aggregate statistics such as total mileage, average trip duration, and average temperature.  

The pivot table below highlights the relationship between these factors. For example, we can observe:  

- Peak travel hours and their corresponding mileage.  
- Variations in average trip durations across the week.  
- The influence of temperature on taxi trip behaviors.  

|   ('avg_duration', 0) |   ('avg_duration', 1) |   ('avg_duration', 2) |   ('avg_duration', 3) |   ('avg_duration', 4) |   ('avg_duration', 5) |   ('avg_duration', 6) |   ('avg_duration', 7) |   ('avg_duration', 8) |   ('avg_duration', 9) |   ('avg_duration', 10) |   ('avg_duration', 11) |   ('avg_duration', 12) |   ('avg_duration', 13) |   ('avg_duration', 14) |   ('avg_duration', 15) |   ('avg_duration', 16) |   ('avg_duration', 17) |   ('avg_duration', 18) |   ('avg_duration', 19) |   ('avg_duration', 20) |   ('avg_duration', 21) |   ('avg_duration', 22) |   ('avg_duration', 23) |   ('avg_temp', 0) |   ('avg_temp', 1) |   ('avg_temp', 2) |   ('avg_temp', 3) |   ('avg_temp', 4) |   ('avg_temp', 5) |   ('avg_temp', 6) |   ('avg_temp', 7) |   ('avg_temp', 8) |   ('avg_temp', 9) |   ('avg_temp', 10) |   ('avg_temp', 11) |   ('avg_temp', 12) |   ('avg_temp', 13) |   ('avg_temp', 14) |   ('avg_temp', 15) |   ('avg_temp', 16) |   ('avg_temp', 17) |   ('avg_temp', 18) |   ('avg_temp', 19) |   ('avg_temp', 20) |   ('avg_temp', 21) |   ('avg_temp', 22) |   ('avg_temp', 23) |   ('total_mileage', 0) |   ('total_mileage', 1) |   ('total_mileage', 2) |   ('total_mileage', 3) |   ('total_mileage', 4) |   ('total_mileage', 5) |   ('total_mileage', 6) |   ('total_mileage', 7) |   ('total_mileage', 8) |   ('total_mileage', 9) |   ('total_mileage', 10) |   ('total_mileage', 11) |   ('total_mileage', 12) |   ('total_mileage', 13) |   ('total_mileage', 14) |   ('total_mileage', 15) |   ('total_mileage', 16) |   ('total_mileage', 17) |   ('total_mileage', 18) |   ('total_mileage', 19) |   ('total_mileage', 20) |   ('total_mileage', 21) |   ('total_mileage', 22) |   ('total_mileage', 23) |
|----------------------:|----------------------:|----------------------:|----------------------:|----------------------:|----------------------:|----------------------:|----------------------:|----------------------:|----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|------------------:|------------------:|------------------:|------------------:|------------------:|------------------:|------------------:|------------------:|------------------:|------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|
|               812.17  |               807.451 |               823.674 |               805.602 |               782.334 |               755.105 |               811.851 |               713.541 |               749.918 |               835.614 |                851.987 |                878.24  |                928.544 |                936.15  |                914.891 |                906.309 |                938.954 |                935.484 |                959.883 |               1008.26  |                998.432 |                962.289 |                883.769 |                836.18  |           16.6752 |           16.8041 |           16.3771 |           15.4748 |           14.7328 |           13.3823 |          11.6798  |           9.03286 |          10.6068  |           13.4427 |            16.5472 |            18.2296 |            19.5495 |            20.1718 |            20.5479 |            20.8016 |            21.0517 |            20.6805 |            20.2378 |            19.6711 |            18.7303 |            17.9048 |            17.0947 |            16.1667 |                15267.4 |               14529.7  |               12955.6  |                7930.48 |                3380.05 |               1769.3   |                918.78  |                1004.51 |                2285.05 |                5451.19 |                 9886.17 |                16889.3  |                 27926.5 |                 36939   |                 39573.3 |                 38609   |                 40744.3 |                 38804.9 |                 38114.3 |                 36506.1 |                 33035.9 |                 31121.4 |                 25868.7 |                 19153.7 |
|               829.906 |               819.637 |               814.341 |               793.092 |               801.616 |               753.438 |               747.848 |               722.828 |               810.58  |               843.897 |                876.207 |                910.427 |                948.641 |                940.033 |                896.611 |                875.96  |                890.945 |                871.193 |                887.058 |                938.621 |                945.988 |                916.898 |                811.906 |                755.855 |           17.1485 |           16.6236 |           16.4475 |           15.098  |           14.6617 |           12.0135 |           9.52812 |           6.6813  |           8.02206 |           11.4612 |            15.9314 |            18.2203 |            19.6582 |            20.9031 |            20.7888 |            21.3096 |            21.5163 |            21.3762 |            20.9901 |            20.2158 |            19.167  |            18.5802 |            17.5279 |            16.4184 |                10933.4 |                9135.57 |                8168.75 |                5252.16 |                3308.02 |               1295.58  |                760.656 |                1073    |                2526.15 |                5055.76 |                 9666.19 |                16352    |                 26032.3 |                 34257.6 |                 36359.6 |                 35246.7 |                 36773.3 |                 36443.4 |                 35395.5 |                 34072.5 |                 29667.6 |                 27480.9 |                 21574.7 |                 15084.5 |
|               842.12  |               835.357 |               876.052 |               833.355 |               795.581 |               776.183 |               776.646 |               791.328 |               793.123 |               836.285 |                849.102 |                785.896 |                769.972 |                812.894 |                819.34  |                846.16  |                866.958 |                887.924 |                885.226 |                898.676 |                872.632 |                867.25  |                855.504 |                835.849 |           16.0873 |           16.1606 |           15.5395 |           15.2515 |           14.5847 |           14.1846 |          13.6561  |          10.8151  |          11.323   |           13.6874 |            15.4037 |            16.8708 |            19.4489 |            20.1808 |            20.4125 |            20.6595 |            20.7819 |            20.839  |            20.3759 |            19.3946 |            18.7406 |            17.1804 |            16.9705 |            15.5712 |                15155.3 |               13693.9  |               13583.8  |                9542.22 |                5867.59 |               3687.62  |               2748.57  |                1554.86 |                1552.05 |                3760.94 |                 6004.11 |                 8055.1  |                 12607.4 |                 18817.7 |                 22168.7 |                 23089.2 |                 24232.9 |                 22937.8 |                 22231.7 |                 22924.1 |                 22632.6 |                 19944.6 |                 19325.4 |                 14463.4 |
|               844.548 |               843.926 |               867.338 |               828.699 |               800.436 |               785.732 |               782.554 |               727.385 |               726.029 |               755.223 |                732.474 |                752.99  |                765.949 |                783.231 |                788.019 |                794.947 |                878.19  |                887.87  |                888.955 |                873.852 |                872.869 |                834.701 |                844.779 |                828.954 |           15.356  |           15.689  |           15.2653 |           13.6967 |           13.6851 |           13.0428 |          12.4722  |           9.60294 |          10.6791  |           12.9561 |            15.0576 |            17.2945 |            18.749  |            20.3214 |            20.4602 |            21.0093 |            21.9725 |            21.3597 |            20.9978 |            20.4154 |            19.7379 |            18.5172 |            18.1035 |            17.021  |                11820.2 |               10582.8  |               11028.6  |                7851.42 |                5096.35 |               3566.62  |               2577.96  |                1513.32 |                1141.37 |                2077.18 |                 3976.24 |                 6265.73 |                 10122.7 |                 14970.8 |                 17078.5 |                 15984.1 |                 19537.9 |                 18408.6 |                 18384.1 |                 17315.3 |                 18744.9 |                 16806.7 |                 14913.7 |                 11410.7 |
|               809.506 |               797.854 |               802.85  |               773.468 |               768.074 |               684.707 |               757.137 |               738.011 |               769.605 |               848.74  |                859.468 |                884.372 |                956.203 |                969.147 |                912.097 |                896.346 |                914.147 |                906.782 |                919.012 |                982.244 |               1027.75  |               1051.27  |                926.574 |                837.427 |           16.664  |           16.6533 |           16.4038 |           14.7203 |           13.4318 |           12.9652 |          10.159   |           6.26795 |           6.57788 |           10.6874 |            14.9677 |            17.9015 |            19.7281 |            20.372  |            20.7603 |            21.4805 |            21.5037 |            21.1113 |            20.7208 |            20.1424 |            19.3075 |            18.646  |            17.9295 |            16.8612 |                16621.1 |               15264.4  |               12053.1  |                6842.54 |                2684.79 |                999.121 |                711.206 |                1273.63 |                2883.53 |                5756.18 |                11089.6  |                20331.7  |                 33990.4 |                 42352.2 |                 44618.5 |                 47410.2 |                 49093.5 |                 45831.1 |                 45198.4 |                 41087.3 |                 36120   |                 34261.5 |                 27202   |                 19707.9 |