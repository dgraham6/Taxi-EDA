<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Title with Image</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
    }
    .page-header {
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
      padding: 20px;
      background-color: #f4f4f4;
      border-bottom: 1px solid #ccc;
    }
    .page-header img {
      max-width: 100px; /* Adjust size as needed */
      border-radius: 50%; /* Makes it circular if the image is square */
      margin-bottom: 15px;
    }
    .page-header h1 {
      margin: 0;
      font-size: 24px;
      color: #333;
    }
    .page-header h2 {
      margin: 0;
      font-size: 18px;
      color: #666;
    }
    .btn {
      text-decoration: none;
      color: white;
      background-color: #007bff;
      padding: 8px 12px;
      border-radius: 4px;
      margin: 5px;
      font-size: 14px;
    }
    .btn:hover {
      background-color: #0056b3;
    }
  </style>
</head>
<body>
  <header class="page-header" role="banner">
    <img src="background.png" alt="Project Logo">
    <h1 class="project-name">Driven to Discover: An Exploratory Data Analysis of Taxi Trips</h1>
    <h2 class="project-tagline">Drake Graham</h2>
    <h3 class="project-tagline">dgraham7362@gmail.com.</h3>
    <a href="hhttps://github.com/dgraham6/Taxi-EDA" class="btn">View on GitHub</a>
    <a href="https://www.linkedin.com/in/drake-graham-a82048240/" class="btn">Linkedin</a>
  </header>
</body>
</html> 

<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Collapsible Sidebar</title>
  <style>
    body {
      font-family: Arial, sans-serif;
    }
    #sidebar {
      width: 250px;
      position: fixed;
      left: 0;
      top: 0;
      bottom: 0;
      background-color: #f4f4f4;
      overflow-y: auto;
      padding: 10px;
      box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
      transition: transform 0.3s ease-in-out;
      transform: translateX(-100%);
    }
    #sidebar.active {
      transform: translateX(0);
    }
    #sidebar h2 {
      font-size: 18px;
      margin-bottom: 10px;
    }
    #sidebar ul {
      list-style: none;
      padding: 0;
    }
    #sidebar ul li {
      margin: 5px 0;
    }
    #sidebar ul li a {
      text-decoration: none;
      color: #333;
    }
    #toggle-btn {
      position: fixed;
      top: 10px;
      left: 10px;
      background-color: #333;
      color: #fff;
      border: none;
      padding: 10px 15px;
      cursor: pointer;
      z-index: 1000;
    }
  </style>
</head>
<body>

<button id="toggle-btn">☰ Table of Contents</button>

<div id="sidebar">
  <h2>Table of Contents</h2>
  <ul>
    <li><a href="#introduction">Introduction</a></li>
    <li><a href="#map-visualization-of-trip-origins">Map Visualization</a></li>
    <li><a href="#data-cleaning">Data Cleaning</a></li>
    <li><a href="#trip-duration-analysis">Trip Duration Analysis</a></li>
    <li><a href="#framing-the-prediction-problem">Prediction Problem</a></li>
    <li><a href="#baseline-model">Baseline Model</a></li>
    <li><a href="#final-model">Final Model</a></li>
  </ul>
</div>

<script>
  const toggleBtn = document.getElementById('toggle-btn');
  const sidebar = document.getElementById('sidebar');

  toggleBtn.addEventListener('click', () => {
    sidebar.classList.toggle('active');
  });
</script>

</body>
</html>


## Introduction  

Did you know you spend around <span style="color:red">37,935</span> hours of your life driving? According to this [estimate](https://www.tempo.io/blog/how-do-people-spend-their-time#:~:text=According%20to%20a%20study%20done%20by%20the%20Harvard%20health), the average American spends a significant amount of their short life driving. 

Computer Science majors spend a lot of time trying to shave off milliseconds from their programs, so it follows that we might also want to spend the time to optimize such a time-consuming activity in all of our lives. If you're like me, you've always been curious about how software like Google Maps, Apple Maps, and Waze estimate ETAs and why there are differences between their given routes. 

In this project, I'll attempt to estimate travel times from a large dataset of completed taxi trips,

I'm using the [Taxi Trips in 2024 in the District of Columbia dataset](https://catalog.data.gov/dataset/taxi-trips-in-2024) from the Department of For-Hire Vehicles, which is intended for public access and use. As of today, there are 10 taxi files combined, totaling over 2 million rows. This data ranges from January 1st, 2024, to November 5th, and it is continuously updated until the year is over. The dataset includes 27 columns, with some of the most relevant for this project being the pickup and drop-off locations, trip duration, and mileage. 

Although I've focused on this specific dataset, my pipeline is adaptable to similar datasets, ensuring it is not limited to this single dataset.

## Map Visualization of Trip Origins  

The map below displays the origins of a sample of 1,000 taxi trips in Washington, D.C., using marker points to represent the geographic distribution.  

<div style="display: flex; justify-content: center; align-items: center; margin: 20px 0;">
  <iframe
    src="map_with_markers.html"
    width="800"
    height="300"
    frameborder="0"
  ></iframe>
</div>

## More Data  

Before diving deep into the taxi log data, we enhance our resources by retrieving hourly weather data using the [Open-Meteo API](https://open-meteo.com/) and performing a left merge with our taxi trip dataset. This additional weather data allows us to incorporate environmental factors, such as temperature, precipitation, and wind speed, that could impact travel times and driving conditions. Below is the two columns that could be very helpful in predicting trip duration.(1)

To further enrich our dataset, we use the [OSRM API](http://project-osrm.org/) to obtain predicted fastest routes and route distances for each trip. This provides a reference for evaluating how the actual taxi routes compare to the optimal routes in terms of distance and duration. By merging these external datasets, we have more data to help deepen our analysis.(2)

              (1)                                                                                (2)
              |       date_time       |   precip(mm) |   snow(in) |                              |   id  |   duration(s) |   distance(m) |
              |-----------------------|-------------:|-----------:|                              |-------|--------------:|--------------:|
              | 2024-01-01 00:00:00  |        0.009 |          0 |                               |    1  |         102.1 |        1000.2 |
              | 2024-01-01 01:00:00  |        0.009 |          0 |                               |    2  |         498.6 |        6198.7 |
              | 2024-01-01 02:00:00  |        0.009 |          0 |                               |    3  |         295.9 |        3659.3 |
              | 2024-01-01 03:00:00  |        0.009 |          0 |                               |    4  |         133.3 |        1647.7 |
              | 2024-01-01 04:00:00  |        0.009 |          0 |                               |    5  |         208.7 |        2348.6 |


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
  width="700"
  height="400"
  frameborder="0"
></iframe>

### README: Average Trip Duration by Day and Hour

This table summarizes the **average trip duration (in minutes)** across different days of the week and hours of the day (0–23). Each row represents a specific day, and each column corresponds to an hour.

#### Key Highlights:
- **Peak Hours**: Longer durations are observed during daytime and evening hours, reflecting higher traffic or demand.
- **Day-to-Day Trends**:
  - **Weekends (Saturday, Sunday)**: Slightly longer average durations during midday.
  - **Weekdays**: Evening hours (e.g., 16–19) show increased durations, especially Thursday and Tuesday.
- **Early Hours**: Minimal durations across all days between 0–5 hours.

This overview helps identify travel patterns and peak times, valuable for optimizing routes or resource allocation.

| **Day**       | **0**  | **1**  | **2**  | **3**  | **4**  | **5**  | **6**  | **7**  | **8**  | **9**  | **10** | **11** | **12** | **13** | **14** | **15** | **16** | **17** | **18** | **19** | **20** | **21** | **22** | **23** |
|---------------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|
| **Friday**    | 12.72  | 12.48  | 12.56  | 12.19  | 11.39  | 10.42  | 10.81  | 11.27  | 12.08  | 13.61  | 13.83  | 14.40  | 15.17  | 15.25  | 14.88  | 14.71  | 15.20  | 15.18  | 15.53  | 16.33  | 16.18  | 15.56  | 14.15  | 13.23  |
| **Monday**    | 13.08  | 12.64  | 12.49  | 12.25  | 12.19  | 10.85  | 10.20  | 11.51  | 13.17  | 13.72  | 14.25  | 14.90  | 15.47  | 15.33  | 14.57  | 14.20  | 14.43  | 14.10  | 14.41  | 15.26  | 15.35  | 14.82  | 13.07  | 12.05  |
| **Saturday**  | 13.16  | 12.99  | 13.42  | 12.45  | 11.71  | 11.39  | 11.32  | 11.35  | 12.44  | 13.61  | 13.85  | 12.79  | 12.55  | 13.21  | 13.22  | 13.62  | 13.98  | 14.32  | 14.30  | 14.52  | 14.13  | 14.00  | 13.71  | 13.05  |
| **Sunday**    | 13.02  | 12.86  | 13.08  | 12.33  | 11.75  | 11.49  | 11.36  | 10.59  | 11.07  | 12.08  | 11.91  | 12.32  | 12.48  | 12.73  | 12.81  | 12.84  | 14.21  | 14.38  | 14.42  | 14.14  | 14.20  | 13.52  | 13.57  | 13.16  |
| **Thursday**  | 12.71  | 12.32  | 12.39  | 11.71  | 11.21  |  9.29  | 10.77  | 11.93  | 12.66  | 13.80  | 14.05  | 14.43  | 15.54  | 15.75  | 14.83  | 14.57  | 14.83  | 14.67  | 14.88  | 15.94  | 16.63  | 16.95  | 14.79  | 13.22  |

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
  - **RMSLE Score:** 0.657 
  - **Why the Improvement:**  
    - New features such as distance, center, and direction added contextual information.  
    - Polynomial features allowed the model to understand non-linear interactions, particularly between distance, time, and other trip-specific variables.  

This improvement demonstrates the importance of thoughtful feature engineering and hyperparameter tuning in creating a model that better aligns with the real-world data generating process.
