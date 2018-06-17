```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

```python
csv_file1 = "raw_data\\city_data.csv"
csv_file2 = "raw_data\\ride_data.csv"

city_df = pd.read_csv(csv_file1)
ride_df = pd.read_csv(csv_file2)
#reading in data files
```

```python
data_df = pd.merge(ride_df,city_df, on='city', how='left')

```

```python
data_df.head()
```

```python
city_group = data_df.groupby(['city'])
city_group_df = pd.DataFrame(city_group.ride_id.count())
city_group_df['Average Fare']= city_group.fare.mean()

city_group_df.columns = ['Total Rides','Average Fare']
city_group_df = city_group_df[['Average Fare','Total Rides']]
city_group_df.head()

```

```python
city_group_df = city_group_df.reset_index()

city_group_df.head()
```

```python
city_group_df_merge = pd.merge(city_group_df,city_df, on='city', how='left')
city_group_df_merge.columns = ['City','Average Fare','Total Rides','Total Drivers','City Type']
#Created new dataframe with columns for bubble chart 

city_group_df_merge.head()
city_group_df_merge['City Type'].value_counts()
#used this for scaling the circle size in the bubble chart
```

```python
city_group_df_merge_urban = city_group_df_merge.loc[city_group_df_merge['City Type']=='Urban',:]
city_group_df_merge_suburban = city_group_df_merge.loc[city_group_df_merge['City Type']=='Suburban',:]
city_group_df_merge_rural = city_group_df_merge.loc[city_group_df_merge['City Type']=='Rural',:]
#created 3 separate dataframes for urban, suburban, and rural
```

```python
plt.scatter(city_group_df_merge_urban['Total Rides'],city_group_df_merge_urban['Average Fare'],c="coral",s=city_group_df_merge['Total Drivers']*(59602/66)/50 , edgecolors="black", alpha=.8)
plt.scatter(city_group_df_merge_suburban['Total Rides'],city_group_df_merge_suburban['Average Fare'],c="skyblue",s=city_group_df_merge['Total Drivers']*(8570/36)/50 , edgecolors="black", alpha=.8)
plt.scatter(city_group_df_merge_rural['Total Rides'],city_group_df_merge_rural['Average Fare'],c="gold",s=city_group_df_merge['Total Drivers']*(537/18)/50 , edgecolors="black", alpha=.8)
#scaled the circle size based on average driver count per city per city type

plt.title('Pyber Ride Sharing Data')
plt.xlabel('Total Number of Rides (Per City)')
plt.ylabel('Average Fare ($)')
plt.legend(('Urban','Suburban','Rural'),loc='upper right',markerscale=.5)
plt.style.use('seaborn-deep')

```

```python
city_type_group = data_df.groupby('type')
city_type_group_df = pd.DataFrame(city_type_group.ride_id.count())
city_type_group_df['Total Fares']=city_type_group.fare.sum()
city_type_group_df['Total Drivers']=city_type_group.driver_count.sum()
#Created dataframe for pie charts

city_type_group_df = city_type_group_df.reset_index()
city_type_group_df.columns = ['City Type','Total Rides','Total Fares', 'Total Drivers']
city_type_group_df.head()
```

```python
explode = (0.05,.05,0.05)
colors = ('gold','skyblue','coral')
plt.pie(city_type_group_df['Total Rides'], explode=explode, labels=city_type_group_df['City Type'], colors=colors, autopct="%1.1f%%", shadow=True,startangle=90)
plt.title("% Total Rides by City Types")
plt.axis('equal')
```

```python
explode = (0.05,.05,0.05)
colors = ('gold','skyblue','coral')
plt.pie(city_type_group_df['Total Fares'], explode=explode, labels=city_type_group_df['City Type'], colors=colors, autopct="%1.1f%%", shadow=True,startangle=90)
plt.title("% Total Fares by City Types")
plt.axis('equal')
```

```python
explode = (0.1,.1,0.1)
colors = ('gold','skyblue','coral')
plt.pie(city_type_group_df['Total Drivers'], explode=explode, labels=city_type_group_df['City Type'], colors=colors, autopct="%1.1f%%", shadow=True,startangle=90)
plt.title("% Total Drivers by City Types")
plt.axis('equal')
```

```python
#Comments-Observable Trends
#The average fares are highest in rural then suburban then urban, which makes sense because the average distance per ride is most likely higher for rural rides. 
#At 86.7%, there are many more urban uber drivers, which makes sense because of the higher density of people in cities
#Suburban has about 1/3 the amount of rides as urban, but has about 1/2 the total fare amount as urban. 
```
