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