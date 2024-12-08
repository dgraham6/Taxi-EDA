# **Exploratory Data Analysis on Taxi Data**  
<center>

Drake Graham  
dgraham7362@gmail.com  

</center>

## Introduction  

Did you know you spend around <span style="color:red">37,935</span> hours of your life driving? According to this [estimate](https://www.tempo.io/blog/how-do-people-spend-their-time#:~:text=According%20to%20a%20study%20done%20by%20the%20Harvard%20health), the average American spends a significant amount of their short life driving. 

Computer Science majors spend a lot of time trying to shave off milliseconds from their programs, so it follows that we might also want to spend the time to optimize such a time-consuming activity in all of our lives. If you're like me, you've always been curious about how software like Google Maps, Apple Maps, and Waze estimate ETAs and why there are differences between their given routes. 

In this project, I'll attempt to estimate travel times from a large dataset of completed taxi trips and compare that to other travel time estimators.

I'm using the [Taxi Trips in 2024 in the District of Columbia dataset](https://catalog.data.gov/dataset/taxi-trips-in-2024) from the Department of For-Hire Vehicles, which is intended for public access and use. As of today, there are 10 taxi files combined, totaling over 2 million rows. This data ranges from January 1st, 2024, to November 5th, and it is continuously updated until the year is over. The dataset includes 27 columns, with some of the most relevant for this project being the pickup and drop-off locations, trip duration, and mileage. 

Although I've focused on this specific dataset, my pipeline is adaptable to similar datasets, ensuring it is not limited to this single dataset.
