# Project Overview
---
This project is an interactive Power BI dashboard that shows us insights derived from a dataset on road accidents in the United Kingdom during the years 2021 and 2022. It shows different types of insights about accident data such as YoY growth, location, time of the day, type of road, type of vehicle and type of area. It also shows a breakdown by severity of the accident and casualties. It also contains filters that help you see insights only for a specific weather condition or road surface type Here is a showcase video of the entire dashboard:


![[2024-08-19 18-56-58.mp4]]
## Steps in Project
---
### Requirement Gathering
---
Clients wants to create a Road Accident Dashboard for the years 2021 and 2022 so that they can have insights on the below requirements:

- Primary KPIs: Total Casualties and Total Accidents values for Current Year and YoY growth
- Primary KPIs: Total Casualties by Accident Severity for Current Year and YoY growth
- Secondary KPIs: Total Casualties with respect to vehicle type for Current Year
- Monthly trend showing comparison of casualties for Current Year and Previous Year
- Casualties by Road Type for Current year
- Current Year Casualties by Area/Location and by Day/Night
- Total Casualties and Total Accidents by Location
### Stakeholders in Project
---
- Ministry of Transport
- Road Transport Department
- Police Force
- Emergency Services Department
- Road Safety Corps
- Transport Operators
- Traffic Management Agencies
- Public 
- Media
### Raw Data Overview
---
The raw data is contained in an Excel .xlsx file. The data is sourced from official UK Road Accident data for the years 2021 and 2022. There is data such as date and time of the accident, exact location, district, time of the day, type of road, type of vehicle and whether it happened in a rural or an urban area. Here is a sample view of the dataset:

![[1446-02-16 13_15_01-Road Accident Data.xlsx - Excel.png]]
### Connecting Data with Power BI
---
To import the data into Power BI, the process is pretty simple:

1. Simply click on "Get Data" in the top ribbon
2. Select "Excel Workbook"
3. Choose the file location where the dataset file was downloaded
4. Select the "Data" sheet, check if the sample data is showing correctly and click "Load"
5. We will have our dataset ready in the Table View of Power BI
### Data Cleaning & Processing
---
In the Power Query Editor, I performed some data cleaning and processing steps, that will be outlined below:
- Corrected a typo in the Accident_Severity column which said "Fetal". Instead replace with "Fatal", which is the correct value.
- Created a Calendar table to make it easier to work with YoY metrics. I Included the next columns derived from the Date column using Time Intelligence functions in DAX: Month, Month Number and Year. The functions used for date formatting are as follows:

Date Column
```DAX
Calendar = CALENDAR(MIN(Data[Accident Date]),MAX(Data[Accident Date]))
```

Month Column
```DAX
Month = FORMAT('Calendar'[Date], "mmm")
```

Month Number Column
```DAX
Month Number = MONTH('Calendar'[Date])
```

Year Column
```DAX
Year = YEAR('Calendar'[Date])
```

### Data Modelling
---
We have 2 tables in this dataset: Data and Calendar. To establish a relationship between these 2 tables, I have connected the Accident Date column from the Data table to the Date column from the Calendar table in a 1 to many relationship. This means that the dates in the Date column are unique, dates are distinct from each other; however, in the Accident Date column, we may have the same date repeated in more than 1 row. This diagram shows the Data Modelling carried out:

![[1446-02-16 15_54_54-Road Accidents UK Dashboard.png]]

## Dashboard Building
---
This Dashboard is composed of 7 visuals/charts, each corresponding to one of the requirements from our stakeholders. 

### KPI Cards - Casualties by Severity + YoY Growth
---
This visual corresponds to the Primary KPIs requirements. Using card visuals I've showcased the number of casualties in 5 different categories, with the corresponding YoY growth calculated for each of them (keep in mind that current year refers to 2022):

#### Current Year Casualties
---

```DAX
Current Year Casualties = TOTALYTD(SUM(Data[Number_of_Casualties]), 'Calendar'[Date])
```

#### Current Year Accidents 
---

```DAX
Current Year Accidents = TOTALYTD(COUNT(Data[Accident_Index]), 'Calendar'[Date])
```

#### Current Year Fatal Casualties
---
Filter Current Year Casualties by Accident_Severity ("Fatal")
#### Current Year Serious Casualties
---
Filter Current Year Casualties by Accident_Severity ("Serious")
#### Current Year Slight Casualties
---
Filter Current Year Casualties by Accident_Severity ("Slight")

#### YoY Accidents: 
---

First, we calculate Previous Year Accidents:

```DAX
Previous Year Accidents = CALCULATE(COUNT(Data[Accident_Index]), SAMEPERIODLASTYEAR('Calendar'[Date]))
```

Then, YoY Accidents:

```DAX
YoY Accidents = ([Current Year Accidents] - [Previous Year Accidents])/[Previous Year Accidents]
```

#### YoY Casualties:
---

First, we calculate Previous Year Casualties:

```DAX
Previous Year Casualties = CALCULATE(SUM(Data[Number_of_Casualties]), SAMEPERIODLASTYEAR('Calendar'[Date]))
```

Then, YoY Casualties:

```DAX
YoY Casualties = ([Current Year Casualties] - [Previous Year Casualties])/[Previous Year Casualties]
```

#### Card Visual Showcase
---

![[1446-02-16 16_09_46-Road Accidents UK Dashboard.png]]

### Casualties by Vehicle Type
---
This table chart shows the number of casualties by each type of vehicle. The vehicle types are the following:

- Agricultural
- Bike
- Bus
- Car
- Other
- Van

To consolidate similar vehicle type and reduce clutter in our visual, I've carried the following Data Cleaning steps to normalize similar vehicle type records. The value groupings are as follows:

- Agricultural: Agricultural vehicle
- Bike: Motorcycle 125cc and under; Motorcycle 50cc and under; Motorcycle over 125cc and up to 50cc; Motorcycle over 500cc; Pedal cycle
- Bus: Bus or coach(17 or more pass seats); Minibus (8-16 passenger seats)
- Car: Car; Taxi/Private hire car
- Other: Other vehicle; Ridden horse
- Van: Goods 7.5 tonnes mgw and over; Goods over 3.5t. and under 7.5t.; Van/Goods 3.5 tonnes mgw or under

![[1446-02-16 16_16_15-Road Accidents UK Dashboard.png]]

### Current Year vs Previous Year Casualties - Monthly Trend
---
Line area chart showcasing the number of casualties per month and comparing the monthly trends differences between the years 2022 and 2021. Formatted using contrasting colors for each year and sorted months in the correct order using the Month Number column from the Calendar table.

![[1446-02-16 16_20_08-Road Accidents UK Dashboard.png]]

### Urban vs Rural
---
Pie chart showing a comparison of the percentages of casualties occurring in an urban environment vs a rural environment.

![[1446-02-16 17_00_48-Road Accidents UK Dashboard.png]]

### Day vs Night
---
Pie chart showing a comparison of the percentages of casualties occurring during daytime vs nighttime. In the original data, the Light_Conditions column has several classifications, but to simplify things, I have grouped them as follows:

- Day: Daylight
- Night: Darkness - lighting unknown; Darkness - lights lit; Darkness - lights unlit; Darkness - no lighting

![[1446-02-16 17_03_50-Road Accidents UK Dashboard.png]]

### Casualties by Road Type
---
Horizontal bar chart showcasing the number of casualties by each type of road.

![[1446-02-16 17_05_06-Road Accidents UK Dashboard.png]]


### Casualties by Location (Map)
---
Created a map visual using the Latitude and Longitude columns to showcase the locations of accident casualties on a map of the UK. To show the values of the map correctly, the Latitude and Longitude averages are represented on the map as dots.

![[1446-02-16 17_07_59-Road Accidents UK Dashboard.png]]
### Filters/Slicers
---
I created 2 filters to change the data displayed in the dashboard, one for Road Surface (Dry, Snow/Ice, Wet) and Weather Conditions (Fine, Other, Rain, Snow/Fog). This means that when we filter by a particular weather condition or type of road surface, only the data corresponding to the value(s) we selected will be shown. Additionally, the visuals interact with each other, so you may also select a specific type of data and the entire dashboard data will filter according to that. For example, if we click on the Urban part of the donut chart, the complete data in the dashboard will change to display only the data related to accidents occurring in an urban environment. This is shown in the showcase video.

![[1446-02-16 17_11_42-Road Accidents UK Dashboard.png]]
