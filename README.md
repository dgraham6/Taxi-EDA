# **Driven to Discover: An Exploratory Data Analysis of Taxi Trips**  
<center>

Drake Graham
<br>
dgraham7362@gmail.com  

</center>

## Introduction  

Did you know you spend around <span style="color:red">37,935</span> hours of your life driving? According to this [estimate](https://www.tempo.io/blog/how-do-people-spend-their-time#:~:text=According%20to%20a%20study%20done%20by%20the%20Harvard%20health), the average American spends a significant amount of their short life driving. 

Computer Science majors spend a lot of time trying to shave off milliseconds from their programs, so it follows that we might also want to spend the time to optimize such a time-consuming activity in all of our lives. If you're like me, you've always been curious about how software like Google Maps, Apple Maps, and Waze estimate ETAs and why there are differences between their given routes. 

In this project, I'll attempt to estimate travel times from a large dataset of completed taxi trips,

I'm using the [Taxi Trips in 2024 in the District of Columbia dataset](https://catalog.data.gov/dataset/taxi-trips-in-2024) from the Department of For-Hire Vehicles, which is intended for public access and use. As of today, there are 10 taxi files combined, totaling over 2 million rows. This data ranges from January 1st, 2024, to November 5th, and it is continuously updated until the year is over. The dataset includes 27 columns, with some of the most relevant for this project being the pickup and drop-off locations, trip duration, and mileage. 

Although I've focused on this specific dataset, my pipeline is adaptable to similar datasets, ensuring it is not limited to this single dataset.

## Map Visualization of Trip Origins  

The map below displays the origins of a sample of 1,000 taxi trips in Washington, D.C., using marker points to represent the geographic distribution.  

<iframe
  src="map_with_markers.html"
  width="500"
  height="400"
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

To further enrich our dataset, we use the [OSRM API](http://project-osrm.org/) to obtain predicted fastest routes and route distances for each trip. This provides a reference for evaluating how the actual taxi routes compare to the optimal routes in terms of distance and duration. By merging these external datasets, we have more data to help deepen our analysis.

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

4. **`ORIGINCITY`**  
   -  Despite a significant portion of this column being NaN and inconsistencies in the input data, this dataset only includes taxi trips from Washington, D.C. Therefore, it is safe to ignore the 0.1% of trips outside the city.


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

| Day of the Week   |   ('avg_duration', 0) |   ('avg_temp', 0) |   ('total_mileage', 0) |   ('total_mileage', 1) |   ('total_mileage', 2) |   ('total_mileage', 3) |   ('total_mileage', 4) |   ('total_mileage', 5) |   ('total_mileage', 6) |   ('total_mileage', 7) |   ('total_mileage', 8) |   ('total_mileage', 9) |   ('total_mileage', 10) |   ('total_mileage', 11) |   ('total_mileage', 12) |   ('total_mileage', 13) |   ('total_mileage', 14) |   ('total_mileage', 15) |   ('total_mileage', 16) |   ('total_mileage', 17) |   ('total_mileage', 18) |   ('total_mileage', 19) |   ('total_mileage', 20) |   ('total_mileage', 21) |   ('total_mileage', 22) |   ('total_mileage', 23) |
|:------------------|----------------------:|------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|-----------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|------------------------:|
| Monday            |                   732 |              19.9 |                   4.62 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |
| Tuesday           |                   934 |              19.9 |                   6.11 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |
| Wednesday         |                    81 |              19.9 |                   0    |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |
| Thursday          |                   450 |              19.9 |                   1.76 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |
| Friday            |                  2228 |              19.9 |                   0.68 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                      0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |                       0 |
## Framing the Prediction Problem  

As stated in the introduction, our goal is to leverage the data a taxi driver would feasibly have at the beginning of a trip to predict the total trip duration, without implementing custom routing algorithms. This aligns with how GPS software like Google Maps estimates trip durations.

### Prediction Problem  
We aim to create a **regression model** that predicts the **trip duration** (in seconds). The model will rely on features available at the time of prediction, such as:  

- **Origin coordinates** (latitude and longitude)  
- **Destination coordinates** 
- **Time of day and day of the week**  
- **Weather conditions** (e.g., temperature, precipitation)  

### Evaluation Metric  
Our chosen evaluation metric is **Root Mean Squared Logarithmic Error (RMSLE)**. This metric is ideal for our problem because:  
- It is robust to outliers and works well with skewed data.  
- It focuses on relative differences rather than absolute ones, which is suitable for predicting durations that can vary widely.  
- It handles non-negative target values effectively, which is appropriate since trip durations cannot be negative.  

Although RMSLE is sensitive to small target values, this is acceptable in our context, as shorter trips are common in urban environments like Washington, D.C., and accurately predicting these is crucial.

### Justification for Feature Selection  
To ensure the model's predictions are realistic and feasible, we only use features that a taxi driver or dispatch service would have access to at the start of the trip. For example:  
- **Weather data** is available from real-time weather APIs.  
- **Time of day** and **day of the week** are trivial to compute from the trip start time.  
- **Geographic coordinates** are often known if the trip is pre-scheduled or provided via a ride-hailing app.  

By adhering to these constraints, our model mimics real-world prediction scenarios and ensures that it is practical for use in applications like GPS software.

## Baseline Model  

To predict taxi trip durations, we developed a **baseline model** using a simple linear regression algorithm with a pipeline in `sklearn`. The pipeline ensures a systematic approach to preprocessing and training, making it easy to extend and refine later.

### Features  
We used two types of features in our baseline model:  

1. **Quantitative Features** (Numerical):  
   - Includes all numerical columns in the dataset.  
   - **Preprocessing:** Missing values were imputed using the mean, and values were scaled using `StandardScaler` to normalize the feature distributions.  

2. **Nominal Features** (Categorical):  
   - Includes all non-numerical columns in the dataset.  
   - **Preprocessing:** Missing values were imputed with the placeholder value `"Unknown"`, and one-hot encoding was applied to convert categories into binary indicators.  

Features dropped from the dataset include:
- `AIRPORT`, as it had too many missing or irrelevant values.  
- `DURATION` and related columns like `log_duration`, since they are directly related to the target variable and would lead to data leakage.

### Model Pipeline  
The pipeline implemented the following steps:  
1. **Preprocessing:**  
   - Numerical features: Imputation and scaling.  
   - Categorical features: Imputation and one-hot encoding.  
2. **Regression Model:**  
   - A simple **Linear Regression** model was chosen to establish a baseline.  

### Evaluation Metric  
We evaluated the model using **Root Mean Squared Logarithmic Error (RMSLE)** to measure performance. RMSLE is well-suited for our task because:  
- It is robust to outliers.  
- It emphasizes relative errors, making it appropriate for predicting highly skewed trip durations.  

### Results and Analysis  
After training the model on 80% of the dataset and testing on the remaining 20%, the baseline model achieved an **RMSLE score of 0.824**.  

This score indicates that, on average, the model's predictions differ from the true values by a factor of approximately **2.28** (calculated as e^0.824). For instance, if the actual trip duration is 10 minutes, the model typically predicts a duration in the range of 4.39 minutes (10 ÷ 2.28) to 22.8 minutes (10 × 2.28).  

This reflects the limitations of the baseline model, which used minimal feature engineering and preprocessing. While it provides a starting point for understanding the data, the RMSLE score highlights significant room for improvement in capturing the complexity of trip duration predictions.

## Final Model  

The **final model** improves upon the baseline by incorporating thoughtfully engineered features and leveraging hyperparameter tuning to refine performance.

### New Features Added  

1. **Geographic Center of Trips (Latitude and Longitude):**  
   - **Reasoning:** This feature represents the midpoint between the origin and destination of each trip.  
   - **Why it’s Good:**  
     - Traffic congestion and road density are often centralized in urban areas. By adding the center point, the model can implicitly account for potential delays in high-traffic zones.
     - This feature captures information that a simple start and endpoint cannot convey.

2. **Trip Direction (Categorical):**  
   - **Reasoning:** Encodes the cardinal direction of the trip, such as NorthEast or SouthWest.  
   - **Why it’s Good:**  
     - Different directions may encounter varying traffic patterns and road conditions (e.g., trips heading downtown may face more congestion than trips leaving the city).
     - Directional information helps the model learn interesitng dependencies without directly relying on longitude and latitude differences.

3. **Trip Distance (Haversine Formula):**  
   - **Reasoning:** Calculates the great-circle distance between the origin and destination points.  
   - **Why it’s Good:**  
     - Distance directly correlates with trip duration; longer distances inherently take more time.
     - The Haversine formula accounts for the curvature of the Earth, providing a precise measurement that is crucial for trips spanning large geographic areas.

4. **Polynomial Features (Degree 2):**  
   - **Reasoning:** Non-linear relationships exist between numerical features (e.g., distance and duration).  
   - **Why it’s Good:**  
     - The squared and interaction terms enable the model to capture non-linear patterns, such as how the duration might increase disproportionately with distance in high-congestion areas.
     - For example, polynomial features allow the model to understand that the impact of traffic on short trips differs from its impact on longer trips.

---

### Modeling Algorithm and Hyperparameter Tuning  

1. **Algorithm:**  
   - The final model used **Linear Regression**.  
   - **Why Linear Regression:**  
     - It provides a transparent and interpretable baseline for understanding the effects of the new features.  
     - Linear Regression pairs well with polynomial features, effectively capturing non-linear trends without the complexity of tree-based models.  

2. **Hyperparameter Tuning:**  
   - **Parameter Tuned:** Degree of polynomial features (`1, 2, 3`).  
   - **Method:** Used `GridSearchCV` with 5-fold cross-validation to identify the optimal degree of polynomial features.  
   - **Best Hyperparameter:** Degree = 2.  
     - Higher degrees (e.g., 3) added unnecessary complexity without improving performance.  
     - Degree 2 captured key non-linear relationships without overfitting.  

---

### Results and Analysis  

- **Baseline Model:**  
  - **RMSLE Score:** 0.824  
  - **Limitations:** Relied on minimal preprocessing and did not incorporate domain-specific knowledge or non-linear relationships.  

- **Final Model:**  
  - **RMSLE Score:** 0.254 
  - **Why the Improvement:**  
    - New features such as distance, center, and direction added contextual information.  
    - Polynomial features allowed the model to understand non-linear interactions, particularly between distance, time, and other trip-specific variables.  

This improvement demonstrates the importance of thoughtful feature engineering and hyperparameter tuning in creating a model that better aligns with the real-world data generating process.
