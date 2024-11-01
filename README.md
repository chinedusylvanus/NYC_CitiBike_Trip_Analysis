# NYC CitiBike Trip Analysis (Using PowerBI)

![](Bikeshare_image.jpg)

## Introduction

I came across the dataset online and admired how rich the data it is, being that I have been trying to get my hands on a good dataset that would serve as a platform to highlight some of my PowerBI skills
This is a power BI project on BikeShare Analysis-NYC Citi Bike showing trips for the year 2016. The project is to analyze and drive insights to answer questions and help the industry make data driven decisions.

## Problem Statement

#### Trip Frequency and Patterns
1. What are the peak hours for bike?
2. How does the number of trips vary by day of the week?
3. How does the number of trips vary by month over the year?


#### Trip Time Analysis
What is the average duration of trips, and how does it vary by day or time?

#### Categorization/Segmentation of Start Station
How can we group the start up stations in terms of average duration per trip and average number of trips per bike, and subsequently drive overall usage/revenue for the business.

## Skills/Concepts demonstrated

The following Power BI features were incorporated:

- DAX
- Measures
- New Column creation
- Date/Time functions
- Dynamic functionality
- Switch measures
- Button

## Data Sourcing

Data was gotten from kaggle, click link (https://www.kaggle.com/datasets/samratp/bikeshare-analysis?select=NYC-CitiBike-2016.csv) to view data.

## Data Modelling

No data modelling was done since the analysis was done using just 1 table.

## Data Transformation

Some of the important transformation steps are highlighted here

### Steps followed 

- Step 1 : Load data into Power BI Desktop, dataset is a csv file (NYC-CitiBike-2016)
- Step 2 : Open power query editor & in view tab under Data preview section, check "column distribution", "column quality" & "column profile" options.
- Step 3 : Also since by default, profile will be opened only for 1000 rows so you need to select "column profiling based on entire dataset".
- Step 4 : It was observed that in none of the columns errors & empty values were present.
- Step 5 : Create new time specific columns on the dataset

          i)   WEEKTYPE = IF(WEEKDAY([starttime])=1,"Weekend",IF(WEEKDAY([starttime])=7,"Weekend","Weekday"))
  
          ii)  WEEKDAY = FORMAT([starttime],"ddd")
  
          iii) MONTHNUMBER = MONTH([starttime])
  
          iv)  HOUR_OF_DAY = IF(HOUR([starttime])=0,"12:00 AM",
                             IF(HOUR([starttime])=1,"1:00 AM",
                             IF(HOUR([starttime])=2,"2:00 AM",
                             IF(HOUR([starttime])=3,"3:00 AM",
                             IF(HOUR([starttime])=4,"4:00 AM",
                             IF(HOUR([starttime])=5,"5:00 AM",
                             IF(HOUR([starttime])=6,"6:00 AM",
                             IF(HOUR([starttime])=7,"7:00 AM",
                             IF(HOUR([starttime])=8,"8:00 AM",
                             IF(HOUR([starttime])=9,"9:00 AM",
                             IF(HOUR([starttime])=10,"10:00 AM",
                             IF(HOUR([starttime])=11,"11:00 AM",
                             IF(HOUR([starttime])=12,"12:00 PM",
                             IF(HOUR([starttime])=13,"1:00 PM",
                             IF(HOUR([starttime])=14,"2:00 PM",
                             IF(HOUR([starttime])=15,"3:00 PM",
                             IF(HOUR([starttime])=16,"4:00 PM",
                             IF(HOUR([starttime])=17,"5:00 PM",
                             IF(HOUR([starttime])=18,"6:00 PM",
                             IF(HOUR([starttime])=19,"7:00 PM",
                             IF(HOUR([starttime])=20,"8:00 PM",
                             IF(HOUR([starttime])=21,"9:00 PM",
                             IF(HOUR([starttime])=22,"10:00 PM",
                             IF(HOUR([starttime])=23,"11:00 PM","Blank"))))))))))))))))))))))))

            v)  HOUR_ID = HOUR([starttime])

            vi) PARTS_OF_DAY = IF(HOUR([starttime])=0,"Night",
                               IF(HOUR([starttime])=1,"Night",
                               IF(HOUR([starttime])=2,"Night",
                               IF(HOUR([starttime])=3,"Night",
                               IF(HOUR([starttime])=4,"Night",
                               IF(HOUR([starttime])=5,"Night",
                               IF(HOUR([starttime])=6,"Morning",
                               IF(HOUR([starttime])=7,"Morning",
                               IF(HOUR([starttime])=8,"Morning",
                               IF(HOUR([starttime])=9,"Morning",
                               IF(HOUR([starttime])=10,"Morning",
                               IF(HOUR([starttime])=11,"Morning",
                               IF(HOUR([starttime])=12,"Afternoon",
                               IF(HOUR([starttime])=13,"Afternoon",
                               IF(HOUR([starttime])=14,"Afternoon",
                               IF(HOUR([starttime])=15,"Afternoon",
                               IF(HOUR([starttime])=16,"Afternoon",
                               IF(HOUR([starttime])=17,"Afternoon",
                               IF(HOUR([starttime])=18,"Evening",
                               IF(HOUR([starttime])=19,"Evening",
                               IF(HOUR([starttime])=20,"Evening",
                               IF(HOUR([starttime])=21,"Evening",
                               IF(HOUR([starttime])=22,"Night",
                               IF(HOUR([starttime])=23,"Night","Blank"))))))))))))))))))))))))

           vii)  Trip Duration_mins = 'NYC-CitiBike-2016'[tripduration_sec]/60

**Calculated columns created

![](Columns_created.PNG)

- Step 6 : Create a new table called SlicerValues (This is what I will be using for my switch measures)

- Step 7 : DAX Calculations - Create a new table called Calculations; this is where all my new measures created would reside for better organization of my report
  
           i)   Total_trips = COUNTROWS('NYC-CitiBike-2016')
           ii)  Average duration/trip = DIVIDE(SUM('NYC-CitiBike-2016'[Trip Duration_mins]),COUNTROWS('NYC-CitiBike-2016'))
           iii) Max Trip Duration = MAX('NYC-CitiBike-2016'[Trip Duration_mins])
           iv)  Min Trip Duration = MIN('NYC-CitiBike-2016'[Trip Duration_mins])
           v)   # Bikes Utilized = DISTINCTCOUNT('NYC-CitiBike-2016'[bikeid])
           vi)  Average No. of Trips/Bike = DIVIDE('CALCULATIONS'[Total_trips],'CALCULATIONS'[# Bikes Utilized])

 
  <p align="center">
  <img src="Capture_DAX.PNG" alt="Chart Image" width="500">
</p>

- Step 8 : Now I created the switch measures

           SW_Measures = 
           VAR Selected_Value = SELECTEDVALUE('SlicerValues'[Value])
           VAR Result = SWITCH(Selected_Value,
                        "Total_trips",[Total_trips],
                        "Average duration/trip",[Average duration/trip],
                        "# Bikes Utilized",[# Bikes Utilized],
                        "Average No. of Trips/Bike",[Average No. of Trips/Bike],
                        BLANK()
           )
           RETURN
           Result

<p align="center">
  <img src="Capture_switch_measure.PNG" alt="Chart Image" width="200">
</p>


- Step 9 : Visualization begins

- Step 10: I created a measure to give each chart title an interactive feature, whereby each chart title changes according to the measure being selected on the swictch slicer

Columnchart Title = SELECTEDVALUE(SlicerValues[Value]) & " by Hour of Day"

Linechart Title = SELECTEDVALUE(SlicerValues[Value]) & " by Month"

Treemap Title = SELECTEDVALUE(SlicerValues[Value]) & " by Weekday"

Below is a screenshot of the report

![](Dashboard.PNG)

[Download the Power BI report](https://github.com/chinedusylvanus/NYC_CitiBike_Trip_Analysis/blob/main/NYC_Citi_Bike.pbix)

## Analysis

#### Trip Frequency and Patterns:

   ![](Capture_totaltrips.PNG)

From the report, it is seen that the busiest starttime of trip on hourly basis fall between 4pm - 7pm with the peak at 5pm for trips made in the year 2016.

Also during weekdays, there seem to be a lot of trips that start at 8am.
Looking at the data by the Days of the week, trips tend to drop on Sunday and monday and starts picking up from Tuesday. The company experiences the highest trips on Wednesday followed by Thursday and Friday.

Total trips by month shows a growth from month to month, with number of trips being at its highest in the month of September, from October we see a drop as we approached the end of the year. This calls for further investigation as to why there was a dip in the last quarter of the year.

Further analysis would need to be done to see if this is a seasonal trend across a 5 year period or just a one off. Also, the cause and proposed solutions should be a thing of interest in the foreseable future.

#### Trip Time Analysis:

   ![](Capture_avgduration.PNG)

Average duration of a trip from our analysis in the year 2016 is 15.81 mins. This varies by day and time as seen in the report.

Trips during the weekends tend to be longer, with an average trip time between these days being above 18.7 mins. Trips on Saturday seem to be the longest with an average of 18.8mins while trips on Tuesday are the shortest with an average time of 14.30mins.

By trip starttime, trips started at 12 am are the longest with an average time above 30mins. While trips immediately after midnight (at 1am) are the shortest with a time of 14.14 mins on the average.


#### Segmentation of Start Station

Using the average of average duration (15.08 mins) as the benchmark for low duration and high duration of trip


<p align="center">
  <img src="Analysis_start_station_segmentation.PNG" alt="Chart Image" width="400">
</p>

<p align="center">
  <img src="Capture_segmentation.PNG" alt="Chart Image" width="400">
</p>


Underutilization in Low-Demand Areas:56% of start stations (358) are characterised with low duration and low trip frequency. This might indicate areas of potential underutilization, suggesting opportunities to either boost demand through promotions or consider redistributing resources if demand remains consistently low.

Occasional Long-Duration Usage:Also, we have 44% of start stations (284) with high-duration, low-frequency trips. This might indicate that bikes are mainly used for infrequent, longer rides. This may reflect leisure-oriented areas or the presence of users who do not depend on bikes for regular commuting but rather for occasional outings.

High Utilization Stations: Lastly, we have 0.2% of stations (just 1) showing high trip frequency with short durations. This station likely serve high-demand, high-turnover areas, possibly in central business hubs or transit-oriented zones. These stations benefit from high visibility and accessibility, indicating success in last-mile connections and short-trip needs.

## Recommendation and Conclusion

### Recommendation



Targeted Marketing During Peak Hours: The highest trip frequency between 4 pm - 7 pm, with a peak at 5 pm, suggests an opportunity to maximize marketing efforts and promotions during this time frame. Consider introducing dynamic pricing or discounts during these hours to capture more demand or encourage off-peak travel.

Weekday Morning Surge Optimization: The noticeable increase in trips at 8 am during weekdays implies a significant commuter demand. Implementing targeted commuter packages or subscriptions for frequent riders during these hours could increase customer loyalty and ridership.

Investigate Q4 Trip Decline: There is a notable drop in trips from October to December, following a peak in September. This trend calls for deeper investigation to identify possible causes (e.g., weather, holiday season changes, operational factors) and potential strategies to mitigate this dip, such as special promotions or seasonal campaigns.

Leverage High Trip Demand on Wednesdays: Wednesday has the highest trip volume, with consistent demand through Thursday and Friday. Introducing mid-week promotions or rewards programs to capitalize on the mid-week peak could sustain and enhance ridership.

Optimize for Weekend Travelers: Since weekend trips are longer, consider offering weekend-specific travel plans, potentially introducing higher fare rates for extended trips or adding special service features tailored to weekend travelers, such as leisure-oriented promotions or family packages.

Lastly, the scatter plot analysis of start stations provides valuable insights into bike usage patterns, highlighting areas of high and low demand. By adjusting bike placement, promoting targeted usage, and supporting high-demand areas, the bikeshare program can optimize resource allocation, improve bike availability, and increase overall user satisfaction.

### Conclusion

The analysis reveals clear trip patterns that can drive strategic decisions.

Evening hours and weekday mornings are critical for targeting commuter traffic. These times represent high-demand windows, offering opportunities to introduce targeted services, pricing adjustments, and marketing strategies to cater to the evening rush and morning commuters.

Mid-Week to Weekend Behavior: Trips increase mid-week, peaking on Wednesday, and maintain high levels through Friday. On weekends, trips are generally longer, suggesting different user behavior between weekdays and weekends. Tailoring services to fit these distinct patterns could enhance customer satisfaction and engagement.

Seasonal Trip Decline Needs Attention:The decline in trips during the last quarter of the year highlights a potential seasonal impact. A longitudinal analysis over a more extended period would help determine if this is an annual occurrence or a one-time anomaly. Understanding the cause and implementing counteractive strategies will be vital in maintaining trip volume stability year-round.

By leveraging insights from this analysis, the company can optimize services, improve customer targeting, and develop strategies to sustain high trip volumes throughout the year.

