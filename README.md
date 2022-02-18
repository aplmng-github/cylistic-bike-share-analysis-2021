# CYCLISTIC BIKE SHARE ANALYSIS: A CAPSTONE STUDY
## How Does a Bike-Share Navigate Speedy Success?

You are a junior data analyst working in the marketing analyst team at Cyclistic, a bike-share company in Chicago. The director of marketing believes the company’s future success depends on maximizing the number of annual memberships. Therefore, your team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, your team will design a new marketing strategy to convert casual riders into annual members. But first, Cyclistic executives must approve your recommendations, so they must be backed up with compelling data insights and professional data visualizations.

### Company Profile
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geo-tracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members.

Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. Although the pricing flexibility helps Cyclistic attract more customers, Moreno believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, Moreno believes there is a very good chance to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their mobility needs.

Moreno has set a clear goal: Design marketing strategies aimed at converting casual riders into annual members. In order to do that, however, the marketing analyst team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. Moreno and her team are interested in analyzing the Cyclistic historical bike trip data to identify trends.

## Phase 1: Ask

Three questions will guide the marketing program:
- How do annual members and casual riders use Cyclistic bikes differently?
- Why would casual riders buy Cyclistic annual memberships?
- How can Cyclistic use digital media to influence casual riders to become members?

**Business Task: Determine the difference on how casual riders and annual members use ridership.**

## Phase 2: Data Preparation

The data used in this study is Cyclistic's historical trip data from the last 12 months (January 2021 to December 2021) from the Motivate International Inc. The data is obtained from this link: https://divvy-tripdata.s3.amazonaws.com/index.html, made available in this license: https://ride.divvybikes.com/data-license-agreement.

Cyclistic historical data is ROCCC.
- **Reliable** - Some columns have missing values, but overall, the data accurately represents the bike rides in Chicago during the 12-month period.
- **Original** - Motivate International Inc., a firm that supervises the bike sharing activities of Divvy, is a reliable primary source.
- **Comprehensive** - The information included in the data are comprehensive as the dates and time, stations, and bike types are included. However, to deep dive on the particular usage of different user types, having demographic profile of each user type is advantageous.
- **Current** - The data is updated and not obsolete. It contains historical data from January 2021 to December 2021.
- **Cited** - The data is valid with a license.

## Phase 3: Data Pre-processing

import pandas as pd
import numpy as np

### Phase 3.1: Data Importation

jan2021 = pd.read_csv('Trips2021/CSV/202101-divvy-tripdata.csv')
feb2021 = pd.read_csv('Trips2021/CSV/202102-divvy-tripdata.csv')
mar2021 = pd.read_csv('Trips2021/CSV/202103-divvy-tripdata.csv')
apr2021 = pd.read_csv('Trips2021/CSV/202104-divvy-tripdata.csv')
may2021 = pd.read_csv('Trips2021/CSV/202105-divvy-tripdata.csv')
jun2021 = pd.read_csv('Trips2021/CSV/202106-divvy-tripdata.csv')
jul2021 = pd.read_csv('Trips2021/CSV/202107-divvy-tripdata.csv')
aug2021 = pd.read_csv('Trips2021/CSV/202108-divvy-tripdata.csv')
sep2021 = pd.read_csv('Trips2021/CSV/202109-divvy-tripdata.csv')
oct2021 = pd.read_csv('Trips2021/CSV/202110-divvy-tripdata.csv')
nov2021 = pd.read_csv('Trips2021/CSV/202111-divvy-tripdata.csv')
dec2021 = pd.read_csv('Trips2021/CSV/202112-divvy-tripdata.csv')

# Counting the observations in each data set
datasets = [jan2021, feb2021, mar2021, apr2021, may2021, jun2021, jul2021, aug2021, sep2021, oct2021, nov2021, dec2021]
def count_rows(data):
    for d in data:
        print(len(d))
        
count_rows(datasets)

### Phase 3.2: Data Exploration

jan2021.info()

#### Checking if the all data frames have the same column names.

dataframes = [jan2021, feb2021, mar2021, apr2021, may2021, jun2021, jul2021, aug2021, sep2021, oct2021, nov2021, dec2021]

if all([set(dataframes[0].columns) == set(df.columns) for df in dataframes]):
    print('All have the same columns.')
else:
    print('Some have different columns.')

### Phase 3.3: Data Wrangling

#### Merging data frames

trips_2021 = pd.concat([jan2021, feb2021, mar2021, apr2021, may2021, jun2021, jul2021, aug2021, sep2021, oct2021, nov2021, dec2021])

#### Getting info from the merged dataframe

trips_2021.info()

#### Checking for duplicates

trips_2021.duplicated().sum()

#### Checking for null values

trips_2021.isnull().sum()

#### Observation
- The entire data frame has 690,809 missing values in `start_station_name` and `start_station_id`.
- The entire data frame has 739,170 missing values in `end_station_name` and `end_station_id`.
- The entire data frame has 4,771 missing values in `end_lat` and `end_lng`.
- The nomenclature of station ids are inconsistent.
- The data type of `started_at` and `ended_at` is object instead of datetime.

#### Cleaning procedure
- Delete rows with missing values.

#### Scope and Delimitations
The data has missing values for `start_station_name` and `start_station_id` mostly on electric_bikes.

#### Deleting rows with null values

trips_2021 = trips_2021.dropna()

#### Checking the number of rows of the filtered dataframe

len(trips_2021)

#### Setting the ride_id as the index

trips_2021.reset_index(inplace=True)

trips_2021.index

trips_2021 = trips_2021.drop('index', axis=1).set_index('ride_id')

trips_2021.head(1)

<font color='dark blue'> **Working with data types**

#### Converting the start_at and ended_at to date_time data type

trips_2021[['started_at', 'ended_at']] = trips_2021[['started_at', 'ended_at']].apply(pd.to_datetime, format='%Y-%m-%d %H:%M:%S.%f')

#### Creating a separate columns for month, day, weekday, and hour

trips_2021['month'] = trips_2021['started_at'].dt.month
trips_2021['day'] = trips_2021['started_at'].dt.day
trips_2021['weekday'] = trips_2021['started_at'].dt.weekday
trips_2021['hour'] = trips_2021['started_at'].dt.hour

#### Add a column name 'ride_length', then calculate the duration of each ride in seconds

trips_2021['ride_length'] = (trips_2021['ended_at'] - trips_2021['started_at']).dt.seconds

#### Checking out if ride_length has negative values

(trips_2021['ride_length'] < 0).value_counts()

trips_2021.info()

#### Preliminary descriptive analysis

trips_2021.groupby('member_casual').agg({'ride_length': [np.nanmean, np.nanmedian, np.nanmax, np.nanmin]})

trips_2021[trips_2021['ride_length'] == 0]

len(trips_2021[trips_2021['ride_length'] == 0])

**Observation:**
- Ride length with 0 seconds are with the same start station and end station. These rows are not useful for further analysis, hence, remove them.

#### Removing invalid data

trips_2021 = trips_2021[trips_2021['ride_length'] != 0]

len(trips_2021)

# Phase 5: Analyze

#### Analyze peak hour for each user type.
- Which hour has the highest number of casual riders?
- Which hour has the highest number of annual members?

rides_per_hour = trips_2021.groupby(['member_casual','hour']).agg({'hour': [np.size]})
rides_per_hour.columns = ['_'.join(colnames) for colnames in rides_per_hour.columns.values]
rides_per_hour.sort_values('hour')

# Assigning a series for each user type
casual_hourly_rides = rides_per_hour.loc['casual']
member_hourly_rides = rides_per_hour.loc['member']

# Assigning a series that contains the hour
hour_x_value = casual_hourly_rides.index
hour_x_value

#### Analyze peak day for each user type.
- Which day of the week has the highest number of casual riders?
- Which day of the week has the highest number of annual members?

# Aggregating user types according to number of rides and average ride duration per each day of the week
rides_per_day = trips_2021.groupby(['member_casual', 'weekday']).agg({'member_casual': [np.size], 'ride_length':[np.nanmean]})
rides_per_day.columns = ['_'.join(colnames) for colnames in rides_per_day.columns.values]

# Assigning a series for each user type
casual_wday_rides = rides_per_day.loc['casual']['member_casual_size'] #casual number of rides
member_wday_rides = rides_per_day.loc['member']['member_casual_size'] #member number of rides

casual_wday_avg_duration = rides_per_day.loc['casual']['ride_length_nanmean'] #casual avg duration per wday
member_wday_avg_duration = rides_per_day.loc['member']['ride_length_nanmean'] #member avg duration per wday

wday_x_value = casual_wday_rides.index
wday_x_value

#### Analyze peak month for each user type.
- Which month has the highest number of casual and member users?
- Which bike type do each usertype prefer?
- What is the monthly average duration for each user type?

rides_per_month = trips_2021.groupby(['member_casual', 'month']).agg({'member_casual': [np.size], 'ride_length':[np.nanmean]})
rides_per_month.columns = ['_'.join(colnames) for colnames in rides_per_month.columns.values]

# Assigning a series by each usertype
casual_monthly_rides = rides_per_month.loc['casual']['member_casual_size']
member_monthly_rides = rides_per_month.loc['member']['member_casual_size']
monthly_x_value = casual_monthly_rides.index

Analyze the bike preference of each user type by weekday.

bike_preference = trips_2021.groupby(['member_casual', 'rideable_type', 'weekday']).agg({'member_casual': [np.size]})
bike_preference.columns = ['_'.join(colnames) for colnames in bike_preference.columns.values]

casual_preference = bike_preference.loc['casual']
member_preference = bike_preference.loc['member']

## Phase 5.1: Visualize

import matplotlib.pyplot as plt
from matplotlib import figure

# Visualize rides per hour
def hourly_rides_viz():
    global hour_x_value
    global casual_hourly_rides
    global member_hourly_rides 
    
    x = hour_x_value
    labels = ['1:00 AM', '2:00 AM', '3:00 AM', '4:00 AM', '5:00 AM', '6:00 AM', '7:00 AM', '8:00 AM', '9:00 AM', '10:00 AM', '11:00 AM', '12:00 PM',
              '1:00 PM', '2:00 PM', '3:00 PM', '4:00 PM', '5:00 PM', '6:00 PM', '7:00 PM', '8:00 PM', '9:00 PM', '10:00 PM', '11:00 PM', '12:00 AM']
    
    plt.plot(hour_x_value, casual_hourly_rides, linewidth=3, color='#94F7AC', label='Casual') 
    plt.plot(hour_x_value, member_hourly_rides, linewidth=3, color='#78959B', label='Member')
    
    plt.title('Casual vs. Member Number of Rides per Hour', fontsize=20)
    plt.xticks(x, labels, rotation=90, ha='center', fontsize=12)
    plt.yticks(fontsize=12)
    plt.xlabel('Hour', fontsize=14)
    plt.ylabel('Number of Rides', fontsize=14)
    plt.legend()
    # plt.grid(color='#E5E5E5')
    
#     plt.rcParams['figure.figsize'] = [15, 7]
    return plt.show()

hourly_rides_viz()

def wday_rides_viz():
    global wday_x_value
    global casual_wday_rides
    global member_wday_rides
    
    x = wday_x_value
    labels = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
    
    plt.plot(x, casual_wday_rides, linewidth=3, color='#94F7AC', label='Casual') ##75BB99
    plt.plot(x, member_wday_rides, linewidth=3, color='#78959B', label='Member') ##8475A2
    
    plt.title('Casual vs. Member Number of Rides per Weekday', fontsize=20)
    plt.xticks(x, labels, ha='center', fontsize=12)
    plt.yticks(fontsize=12)
    plt.xlabel('Weekday', fontsize=14)
    plt.ylabel('Number of Rides', fontsize=14)
    plt.legend()

    plt.rcParams['figure.figsize'] = [15, 7]
    return plt.show()

wday_rides_viz()

- Get the series of ride length.
- Plot as bar graph.
- Highlight the highest duration.

def wday_avg_duration():
    global casual_wday_avg_duration
    global member_wday_avg_duration
    global wday_x_value
    
    x_indexes = np.arange(len(wday_x_value))
    labels = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
    gap = 0.125

    plt.bar(x_indexes-gap, casual_wday_avg_duration, width=0.25, color='#94F7AC', label='Casual')
    plt.bar(x_indexes+gap, member_wday_avg_duration, width=0.25, color='#78959B',label='Member')
    
    plt.title('Casual vs. Member Average Duration per Weekday', fontsize=20)
    plt.xticks(x_indexes, labels, ha='center', fontsize=12)
    plt.yticks(fontsize=12)
    plt.xlabel('Weekday', fontsize=14)
    plt.ylabel('Average Duration', fontsize=14)
    plt.legend()
    

    return plt.show()

wday_avg_duration()

def monthly_rides_viz():
    global monthly_x_value
    global casual_monthly_rides
    global member_monthly_rides
    
    x = monthly_x_value
    labels = ['January', 'February', 'March', 'April', 'May', 'June',
              'July', 'August', 'September', 'October', 'November', 'December']
    
    plt.plot(monthly_x_value, casual_monthly_rides, linewidth=3, color='#94F7AC', label='Casual')
    plt.plot(monthly_x_value, member_monthly_rides, linewidth=3, color='#78959B', label='Member')
    
    plt.title('Casual vs. Member Number of Rides per Month', fontsize=20)
    plt.xticks(x, labels, ha='center', fontsize=12)
    plt.yticks(fontsize=12)
    plt.xlabel('Month', fontsize=14)
    plt.ylabel('Number of Rides', fontsize=14)
    plt.legend()

    plt.rcParams['figure.figsize'] = [15, 7]
    return plt.show()

monthly_rides_viz()

def monthly_avg_duration():
    global monthly_x_value
    global casual_monthly_rides
    global member_monthly_rides
    
    x_indexes = np.arange(len(monthly_x_value))
    labels = ['January', 'February', 'March', 'April', 'May', 'June',
              'July', 'August', 'September', 'October', 'November', 'December']
    gap = 0.15

    plt.bar(x_indexes-gap, casual_monthly_rides, width=0.30, color='#94F7AC', label='Casual')
    plt.bar(x_indexes+gap, member_monthly_rides, width=0.30, color='#78959B', label='Member')
    plt.title('Casual vs. Member Average Duration per Month', fontsize=20)
    plt.xticks(x_indexes, labels, ha='center', fontsize=12)
    plt.yticks(fontsize=12)
    plt.xlabel('Month', fontsize=14)
    plt.ylabel('Average Duration', fontsize=14)
    plt.legend()
    

    return plt.show()

monthly_avg_duration()

def casual_bikes():
    global casual_preference
    
    x_indexes = np.arange(len(wday_x_value))
    labels = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
    
    x = casual_preference.index.get_level_values(1).unique()
    cb = casual_preference.loc['classic_bike']['member_casual_size']
    db = casual_preference.loc['docked_bike']['member_casual_size']
    eb = casual_preference.loc['electric_bike']['member_casual_size']
    bottom_ebike = np.add(cb,db)
    
    plt.bar(x, cb, width= 0.5, color='#4D78A2', label='Classic bike') #blue
    plt.bar(x, db, width= 0.5, bottom=cb, color='#B2E8DC', label='Docked bike') #mint green
    plt.bar(x, eb, width= 0.5, bottom=bottom_ebike, color='#8CA2AB', label='Electric bike') #bluish grey
    
    plt.title('Casual\'s Bike Preference Percentage Share per Weekday', fontsize=20)
    plt.xlabel('Weekday', fontsize=14)
    plt.ylabel('Number of Bike Rides', fontsize=14)
    plt.xticks(x_indexes, labels, ha='center', fontsize=12)
    plt.yticks(fontsize=12)
    plt.legend()
    return plt.show()

casual_bikes()

def member_bikes():
    global member_preference
    
    x_indexes = np.arange(len(wday_x_value))
    labels = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
    
    x = member_preference.index.get_level_values(1).unique()
    cb = member_preference.loc['classic_bike']['member_casual_size']
    db = member_preference.loc['docked_bike']['member_casual_size']
    eb = member_preference.loc['electric_bike']['member_casual_size']
    bottom_bike = np.add(eb,db)
    
    plt.bar(x, cb, width= 0.5, color='#4D78A2', label='Classic bike')
    plt.bar(x, db, width= 0.5, bottom=bottom_bike, color='#B2E8DC', label='Docked bike')
    plt.bar(x, eb, width= 0.5, bottom=cb, color='#8CA2AB', label='Electric bike')
    
    plt.title('Member\'s Bike Preference Percentage Share per Weekday', fontsize=20)
    plt.xlabel('Weekday', fontsize=14)
    plt.ylabel('Number of Bike Rides', fontsize=14)
    plt.xticks(x_indexes, labels, ha='center', fontsize=12)
    plt.yticks(fontsize=12)
    plt.legend()
    return plt.show()

member_bikes()





