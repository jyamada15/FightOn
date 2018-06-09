

```python
import pandas as pd
```


```python
file = "purchase_data.json"
df = pd.read_json(file)
df.head();
```


```python
#print('Total Number of Players: '+ str(df['SN'].nunique()))
total_players = df['SN'].nunique()
df_total_players = pd.DataFrame({'Total Players':[total_players]})
df_total_players
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_unique= df.drop_duplicates(subset='SN', keep='first',inplace=False)
df_unique.describe();

```


```python
Number_Of_Unique_Items = df['Item ID'].nunique()
Average_Price = df.Price.mean()
Number_Of_Purchases = df.Price.count()
Total_Revenue = df.Price.sum()

df_purchasing_analysis = pd.DataFrame({'Number of Unique Items':[Number_Of_Unique_Items],'Average Price':[Average_Price],'Number of Purchases':[Number_Of_Purchases],'Total Revenue':[Total_Revenue]})
cols = list(df_purchasing_analysis.columns.values)
df_purchasing_analysis = df_purchasing_analysis[['Number of Unique Items','Average Price',
 'Number of Purchases',
 'Total Revenue']]
df_purchasing_analysis['Average Price']=df_purchasing_analysis['Average Price'].map("${:,.2f}".format)
df_purchasing_analysis['Total Revenue']=df_purchasing_analysis['Total Revenue'].map("${:,.2f}".format)

df_purchasing_analysis
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2,286.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_gender = df_unique.groupby(['Gender'])
df_comparison = pd.DataFrame(df_gender['Gender'].count()/total_players*100)
df_comparison['Total Count']=df_gender['Gender'].count()
df_comparison;
```


```python
df_comparison = df_comparison.rename(columns={"Gender":"Percentage of Players"})
df_comparison.reset_index(inplace=True)

df_comparison;
```


```python
df_comparison['Percentage of Players']=df_comparison['Percentage of Players'].map("{:,.2f}".format)


```


```python
df_comparison = df_comparison.sort_values(by='Total Count',ascending=False)
df_comparison
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Male</td>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Female</td>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_gender_all = df.groupby(['Gender'])
```


```python
df_purchasing_gender = pd.DataFrame(df_gender_all['SN'].count())
df_purchasing_gender = df_purchasing_gender.rename(columns = {"SN": "Purchase Count"})
df_purchasing_gender['Average Purchase Price'] = df_gender_all.Price.mean()
df_purchasing_gender['Total Purchase Value'] = df_gender_all.Price.sum()
df_purchasing_gender['Normalized Totals'] = df_gender_all.Price.mean()*(780/573)

df_purchasing_gender;

```


```python
df_purchasing_gender[['Average Purchase Price','Total Purchase Value', 'Normalized Totals']]=df_purchasing_gender[['Average Purchase Price','Total Purchase Value', 'Normalized Totals']].applymap("${:,.2f}".format)
df_purchasing_gender
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1,867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.42</td>
    </tr>
  </tbody>
</table>
</div>




```python
bins = [0,9,14,19,24,29,34,39,100]
group_names = ["<10","10-14","15-19","20-24","25-29","30-34","35-39","40+"]
df_unique["Bins"]=pd.cut(df_unique['Age'], bins, labels = group_names)

df_age_demographics_group = df_unique.groupby(['Bins'])
df_age_demographics = pd.DataFrame(df_age_demographics_group.count())
df_age_demographics = df_age_demographics[['Age']]
#df.sort_values(by='Age', ascending = True)
df_age_demographics['Percentage of Players']=df_age_demographics['Age']/total_players*100

df_age_demographics;
```

    C:\Users\Jeff\Anaconda3\lib\site-packages\ipykernel_launcher.py:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      This is separate from the ipykernel package so we can avoid doing imports until
    


```python
df_age_demographics.columns = ['Total Count','Percentage Of Players']
df_age_demographics = df_age_demographics[['Percentage Of Players','Total Count']]
df_age_demographics['Percentage Of Players']=df_age_demographics['Percentage Of Players'].map("{:,.2f}".format)

df_age_demographics.reset_index(inplace=True)

df_age_demographics
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Bins</th>
      <th>Percentage Of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>&lt;10</td>
      <td>3.32</td>
      <td>19</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10-14</td>
      <td>4.01</td>
      <td>23</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15-19</td>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20-24</td>
      <td>45.20</td>
      <td>259</td>
    </tr>
    <tr>
      <th>4</th>
      <td>25-29</td>
      <td>15.18</td>
      <td>87</td>
    </tr>
    <tr>
      <th>5</th>
      <td>30-34</td>
      <td>8.20</td>
      <td>47</td>
    </tr>
    <tr>
      <th>6</th>
      <td>35-39</td>
      <td>4.71</td>
      <td>27</td>
    </tr>
    <tr>
      <th>7</th>
      <td>40+</td>
      <td>1.92</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>




```python
df["Bins"]=pd.cut(df['Age'], bins, labels = group_names)

df_age_demographics_group_age = df.groupby(['Bins'])
df_age_demographics_age = pd.DataFrame(df_age_demographics_group_age.count())
df_age_demographics_age = df_age_demographics_age[['Age']]

df_age_demographics_age["C2"]=df_age_demographics_group_age.Price.mean()
df_age_demographics_age["C3"]=df_age_demographics_group_age.Price.sum()
df_age_demographics_age["C4"]=df_age_demographics_group_age.Price.mean()*(780/573)

df_age_demographics_age.columns = ['Purchase Count', 'Average Purchase Price','Total Purchase Value','Normalized Totals']
df_age_demographics_age[[ 'Average Purchase Price','Total Purchase Value','Normalized Totals']]=df_age_demographics_age[['Average Purchase Price','Total Purchase Value','Normalized Totals']].applymap("${:,.2f}".format)

df_age_demographics_age


```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Bins</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$4.06</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$3.77</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.96</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.97</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.03</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$3.87</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.30</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_top_spenders_group = df.groupby(['SN'])

df_top_spenders = pd.DataFrame(df.SN.value_counts().head())
df_top_spenders['Average Purchase Price'] = df_top_spenders_group.Price.mean()
df_top_spenders['Total Purchase Value'] = df_top_spenders_group.Price.sum()
df_top_spenders[[ 'Average Purchase Price','Total Purchase Value']]=df_top_spenders[['Average Purchase Price','Total Purchase Value']].applymap("${:,.2f}".format)

df_top_spenders
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SN</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Sondastan54</th>
      <td>4</td>
      <td>$2.56</td>
      <td>$10.24</td>
    </tr>
    <tr>
      <th>Hailaphos89</th>
      <td>4</td>
      <td>$1.47</td>
      <td>$5.87</td>
    </tr>
    <tr>
      <th>Qarwen67</th>
      <td>4</td>
      <td>$2.49</td>
      <td>$9.97</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_popular_items= df.loc[:,['Item ID', 'Item Name', 'Price']]
df_popular_items_group=df_popular_items.groupby(['Item ID', 'Item Name']).count()

df_popular_items_group['Item Price']= df_popular_items.groupby(['Item ID', 'Item Name']).Price.mean()
df_popular_items_group['Total Purchase Value']= df_popular_items.groupby(['Item ID', 'Item Name']).Price.sum()

df_popular_items_group = df_popular_items_group.rename(columns={"Price":"Purchase Count"})
df_popular_items_group = df_popular_items_group.sort_values(by='Purchase Count',ascending=False)
df_popular_items_group[['Item Price', 'Total Purchase Value']]=df_popular_items_group[['Item Price', 'Total Purchase Value']].applymap("${:,.2f}".format)
df_popular_items_group.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_popular_items_profit= df.loc[:,['Item ID', 'Item Name', 'Price']]
df_popular_items_group_profit=df_popular_items_profit.groupby(['Item ID', 'Item Name']).count()

df_popular_items_group_profit['Item Price']= df_popular_items_profit.groupby(['Item ID', 'Item Name']).Price.mean()
df_popular_items_group_profit['Total Purchase Value']= df_popular_items_profit.groupby(['Item ID', 'Item Name']).Price.sum()

df_popular_items_group_profit = df_popular_items_group_profit.rename(columns={"Price":"Purchase Count"})
df_popular_items_group_profit = df_popular_items_group_profit.sort_values(by='Total Purchase Value',ascending=False)
df_popular_items_group_profit[['Item Price', 'Total Purchase Value']]=df_popular_items_group_profit[['Item Price', 'Total Purchase Value']].applymap("${:,.2f}".format)
df_popular_items_group_profit.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


