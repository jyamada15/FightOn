```python
import json
import requests
from pprint import pprint
from config import api_key
import random
from citipy import citipy
import pandas as pd
import matplotlib.pyplot as plt
import os
import csv
```

```python
rand_latitude = []
rand_longitude = []
total_number = 2000

#generate random latitude and longitude coordinates
for x in range(total_number):
    rand_latitude.append(random.uniform(-90.00,90.00))
    rand_longitude.append(random.uniform(-180,180))
    
```

```python
city_name = []
city_code = []

#use citipy to find closest cities
for y in range(total_number):
    city = citipy.nearest_city(rand_latitude[y],rand_longitude[y])
    city_name.append(city.city_name)
    city_code.append(city.country_code)
    

```

```python
#create empty dataframe with column headers
city_info_df = pd.DataFrame(columns=['City Name','City Country','Date','Latitude','Longitude','Temperature (F)',
                                     'Humidity (%)', 'Cloudiness (%)','Wind Speed (mph)'])

#assign arrays to dataframe
city_info_df['City Name']=city_name
city_info_df['City Country']=city_code
city_info_df['Latitude']=rand_latitude
city_info_df['Longitude']=rand_longitude

#reset index of dataframe and drop duplicates
city_info_df_nodupes = city_info_df.drop_duplicates(subset = 'City Name', keep='first',inplace=False)
city_info_df_nodupes=city_info_df_nodupes.reset_index(drop=True)

```

```python
url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"

new_size=len(city_info_df_nodupes)

print('Beginning Data Retrieval')
print('-------------------------------------------')

#loop through dataframe calling api and storing results in dataframe
for z in range(len(city_info_df_nodupes)):
    try:
        city = city_info_df_nodupes['City Name'][z]
    except KeyError:
        print(f'Processing record: {z+1}/{new_size} - {city},{city_country}')
        continue
    city_country = city_info_df_nodupes['City Country'][z]
    query_url = f"{url}appid={api_key}&q={city}&,{city_country}&units={units}"
    weather_request = requests.get(query_url)
    response_json = weather_request.json()
    
    if response_json['cod']=='404':
        city_info_df_nodupes.iloc[z,city_info_df_nodupes.columns.get_loc('Temperature (F)')]=float('NaN')
        city_info_df_nodupes.iloc[z,city_info_df_nodupes.columns.get_loc('Humidity (%)')]=float('NaN')
        city_info_df_nodupes.iloc[z,city_info_df_nodupes.columns.get_loc('Cloudiness (%)')]=float('NaN')
        city_info_df_nodupes.iloc[z,city_info_df_nodupes.columns.get_loc('Wind Speed (mph)')]=float('NaN')
        city_info_df_nodupes.iloc[z,city_info_df_nodupes.columns.get_loc('Date')]=float('NaN')
        print(f'Processing record: {z+1}/{new_size} - {city},{city_country}')
    else:
        city_info_df_nodupes.iloc[z,city_info_df_nodupes.columns.get_loc('Temperature (F)')]=response_json['main']['temp_max']
        city_info_df_nodupes.iloc[z,city_info_df_nodupes.columns.get_loc('Humidity (%)')]=response_json['main']['humidity']
        city_info_df_nodupes.iloc[z,city_info_df_nodupes.columns.get_loc('Cloudiness (%)')]=response_json['clouds']['all']
        city_info_df_nodupes.iloc[z,city_info_df_nodupes.columns.get_loc('Wind Speed (mph)')]=response_json['wind']['speed']
        city_info_df_nodupes.iloc[z,city_info_df_nodupes.columns.get_loc('Date')]=response_json['dt']

        print(f'Processing record: {z+1}/{new_size} - {city},{city_country}')

print('-------------------------------------------')
print('End of Data Retrieval')
    
    
```

```python
#drop NaNs in dataframe and reset index
city_info_df_nodupes_postapi = city_info_df_nodupes.dropna()
city_info_df_nodupes_postapi = city_info_df_nodupes_postapi.reset_index(drop=True)
```

```python
#create scatterplot with temp and latitude
temp_scatter = plt.scatter(city_info_df_nodupes_postapi['Latitude'],
            city_info_df_nodupes_postapi['Temperature (F)'], c="coral",edgecolors="black", alpha=.8)
plt.title('City Latitude vs Max Temperature (F)')
plt.xlabel('City Latitude')
plt.ylabel('Temperature (F)')
plt.xlim(-90,90)

plt.style.use('seaborn')
plt.savefig('Latitude_vs_Temp')
```

```python
#create scatterplot with humidity and latitude

humid_scatter = plt.scatter(city_info_df_nodupes_postapi['Latitude'],
            city_info_df_nodupes_postapi['Humidity (%)'], c="skyblue",edgecolors="black", alpha=.9)
plt.title('City Latitude vs Humidity (%)')
plt.xlabel('City Latitude')
plt.ylabel('Humidity (%)')

plt.xlim(-90,90)
plt.ylim(0,102)

plt.style.use('seaborn')

plt.savefig('Latitude_vs_Humidity')
```

```python
#create scatterplot with cloudiness and latitude

cloud_scatter = plt.scatter(city_info_df_nodupes_postapi['Latitude'],
            city_info_df_nodupes_postapi['Cloudiness (%)'], c="gold",edgecolors="black", alpha=.9)
plt.title('City Latitude vs Cloudiness (%)')
plt.xlabel('City Latitude')
plt.ylabel('Cloudiness (%)')

plt.xlim(-90,90)
plt.ylim(-2,102)

plt.style.use('seaborn')
plt.savefig('Latitude_vs_Cloudiness')
```

```python
#create scatterplot with wind speed and latitude

wind_scatter = plt.scatter(city_info_df_nodupes_postapi['Latitude'],
            city_info_df_nodupes_postapi['Wind Speed (mph)'], c="magenta",edgecolors="black", alpha=.9)
plt.title('City Latitude vs Wind Speed (Mph)')
plt.xlabel('City Latitude')
plt.ylabel('Wind Speed (Mph)')

plt.xlim(-90,90)


plt.style.use('seaborn')
plt.savefig('Latitude_vs_Wind_Speed')
```

```python
#Observations:
# The hottest cities (>90 degrees) have a mean latitude of 23; lying between 0 and 40 latitude/ mean longitude of 25; lying between -5 and 75.
# Cloud coverage and wind speed doesn't seem to have a correlation in relation to the equator
# The least humid places are between 15 and 40 latitude

#created dataframe to look at the relationship of hot cities
hot_cities_df=city_info_df_nodupes_postapi.loc[city_info_df_nodupes_postapi['Temperature (F)']>90,:]
hot_cities_df.describe()

```

```python
#create scatterplot for hot cities with latitude and longitude

plt.scatter(hot_cities_df['Latitude'],
            hot_cities_df['Longitude'], c="red",edgecolors="black", alpha=.7)
plt.title('Hot Cities Latitude vs Longitude (>90 degrees)')
plt.xlabel('City Latitude')
plt.ylabel('Longitude')

plt.xlim(-90,90)
plt.ylim(-180,180)
```

```python
#generate output file with results
city_info_df_nodupes_postapi.to_csv('api_output.csv', sep=',')
```

```python
#.gitignore
```
