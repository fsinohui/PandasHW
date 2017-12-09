# PandasHW
Heroes of Pymoli Data Analysis
1. Most of the players are male, at Least 80% but per capita they only spend slightly more than females. 
2. Most of their players are between the age of 15-24 but no matter the age the spending habits are the same
3. Only 2 of the most popular items are in the top five most lucrative


```python
import pandas as pd
import numpy as np
import json
```


```python
#path of .json file
path_purch_data1='resources/purchase_data.json'
path_purch_data2='resources/purchase_data2.json'
```


```python
# Open .json read and convert to df
with open(path_purch_data1) as f:
   data = json.load(f)
purch_df = pd.DataFrame(data)

with open(path_purch_data2) as f:
   data2 = json.load(f)
purch2_df = pd.DataFrame(data2)
```


```python
purch_df = purch_df.append(purch2_df)
purch_df = purch_df.reset_index(drop=True)
#purch_df.head(2)
```


```python
# List Headers for Reference
#list(purch_df)
```


```python
# Export to Excel to check answers
writer = pd.ExcelWriter('Heroes_of_Pymoli.xlsx')
purch_df.to_excel(writer,'Data')
writer.save()
```


```python
player_cnt = len(purch_df.groupby('SN'))
player_cnt_lst = {'Total_Players': [player_cnt]}
player_cnt_df = pd.DataFrame(data=player_cnt_lst)
player_cnt_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total_Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>612</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Purchasing Analysis (total)
item_cnt = len(purch_df.groupby('Item ID'))
avg_purch_price = purch_df['Price'].mean()
total_purch_cnt = len(purch_df)
total_revenue = purch_df['Price'].sum()
purch_total = {'Item Count':[item_cnt], 'Avg Purchase Price':[avg_purch_price],
              'Total \nPurchase \nCount':[total_purch_cnt],'Total\nRevenue':[total_revenue]}
purch_total_df = pd.DataFrame(data=purch_total)
purch_total_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Avg Purchase Price</th>
      <th>Item Count</th>
      <th>Total
Revenue</th>
      <th>Total 
Purchase 
Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2.930571</td>
      <td>184</td>
      <td>2514.43</td>
      <td>858</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Isolate gender column and groupby
gender = purch_df[['SN', 'Gender']]
gender_df = gender.groupby('SN').first()
```


```python
# Gender counts
male_cnt = len(gender_df.loc[gender_df['Gender'] == 'Male'])
female_cnt = len(gender_df.loc[gender_df['Gender'] == 'Female'])
other_cnt = len(gender_df.loc[gender_df['Gender'] == 'Other / Non-Disclosed'])
# Gender Percent
male_pct = male_cnt/player_cnt
female_pct = female_cnt/player_cnt
other_pct = other_cnt/player_cnt
```


```python
# Create gender demographics df
gender_demographics = {'Male':[male_pct],'Female':[female_pct],'Other':[other_pct]}
gender_demographics_df = pd.DataFrame(data=gender_demographics)
gender_demographics_df['Male'] = pd.Series(["{0:.2f}%".format(val * 100) for val in gender_demographics_df['Male']], index = gender_demographics_df.index)
gender_demographics_df['Female'] = pd.Series(["{0:.2f}%".format(val * 100) for val in gender_demographics_df['Female']], index = gender_demographics_df.index)
gender_demographics_df['Other'] = pd.Series(["{0:.2f}%".format(val * 100) for val in gender_demographics_df['Other']], index = gender_demographics_df.index)
gender_demographics_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Female</th>
      <th>Male</th>
      <th>Other</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>17.65%</td>
      <td>80.88%</td>
      <td>1.47%</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Gender Purchases
male_purch_df = purch_df.loc[purch_df['Gender'] == 'Male']
female_purch_df = purch_df.loc[purch_df['Gender'] == 'Female']
other_purch_df = purch_df.loc[purch_df['Gender'] == 'Other / Non-Disclosed']

# Gender price avg
male_avg_purch = male_purch_df['Price'].mean()
female_avg_purch = female_purch_df['Price'].mean()
other_avg_purch = other_purch_df['Price'].mean()

# Gender total purchase value
male_total_purch = male_purch_df['Price'].sum()
female_total_purch = female_purch_df['Price'].sum()
other_total_purch = other_purch_df['Price'].sum()

#Gender Normalized purchase Value
male_Ntotal_purch = male_total_purch/male_cnt
female_Ntotal_purch = female_total_purch/female_cnt
other_Ntotal_purch = other_total_purch/other_cnt
```


```python
#Purchase Analysis (Gender)
purch_analysis_gender = {'Gender':['Male','Female','Other'],
                            'Purchase Count':[male_cnt, female_cnt, other_cnt],
                            'Average Purchase':[male_avg_purch, female_avg_purch, other_avg_purch],
                            'Total Purchase Value':[male_total_purch, female_total_purch, other_total_purch],
                            'Normalized Purchase Value':[male_Ntotal_purch, female_Ntotal_purch, other_Ntotal_purch]}
purch_analysis_gender_df = pd.DataFrame(data=purch_analysis_gender)
```


```python
purch_analysis_gender_df = purch_analysis_gender_df[['Gender', 'Purchase Count','Average Purchase', 'Total Purchase Value','Normalized Purchase Value']]
purch_analysis_gender_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Purchase Count</th>
      <th>Average Purchase</th>
      <th>Total Purchase Value</th>
      <th>Normalized Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Male</td>
      <td>495</td>
      <td>2.944448</td>
      <td>2052.28</td>
      <td>4.146020</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Female</td>
      <td>108</td>
      <td>2.847584</td>
      <td>424.29</td>
      <td>3.928611</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other</td>
      <td>9</td>
      <td>3.155000</td>
      <td>37.86</td>
      <td>4.206667</td>
    </tr>
  </tbody>
</table>
</div>




```python
bins = [0, 10, 15, 20, 25, 30, 35, 60]

# Create the names for the four bins
group_names = ['<10', '10-14', '15-19','20-24', '25-29', '30-34','>34']
purch_df['Age_Bin'] = pd.cut(purch_df["Age"], bins, labels=group_names)
```


```python
#Aggregate Data for bins
purch_age_df = pd.DataFrame(purch_df.groupby(['Age_Bin'], as_index=False)
                        .agg({'Gender':'count', "Price": [np.mean, np.sum]})
                        )
purch_age_df.columns = purch_age_df.columns.droplevel(1)
```


```python
purch_age2_df = pd.DataFrame(purch_df.groupby(['Age_Bin'], as_index=False)['Price'].sum()
                            .rename(columns={'Price':'Total'}))

merge_age_df = (pd.merge(purch_age_df,purch_age2_df, how='outer',on = 'Age_Bin')
                .rename(columns={"Gender":"Count",'Price':'Mean'})
               )

merge_age_df.columns = ['Age_Bin','Count','Mean', 'Sum', 'Total']
merge_age_df['NTotal'] = merge_age_df['Total']/merge_age_df['Count']
merge_age_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age_Bin</th>
      <th>Count</th>
      <th>Mean</th>
      <th>Sum</th>
      <th>Total</th>
      <th>NTotal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>&lt;10</td>
      <td>42</td>
      <td>2.958571</td>
      <td>124.26</td>
      <td>124.26</td>
      <td>2.958571</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10-14</td>
      <td>86</td>
      <td>2.890349</td>
      <td>248.57</td>
      <td>248.57</td>
      <td>2.890349</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15-19</td>
      <td>224</td>
      <td>2.848750</td>
      <td>638.12</td>
      <td>638.12</td>
      <td>2.848750</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20-24</td>
      <td>371</td>
      <td>2.974259</td>
      <td>1103.45</td>
      <td>1103.45</td>
      <td>2.974259</td>
    </tr>
    <tr>
      <th>4</th>
      <td>25-29</td>
      <td>84</td>
      <td>2.873333</td>
      <td>241.36</td>
      <td>241.36</td>
      <td>2.873333</td>
    </tr>
    <tr>
      <th>5</th>
      <td>30-34</td>
      <td>72</td>
      <td>2.933333</td>
      <td>211.20</td>
      <td>211.20</td>
      <td>2.933333</td>
    </tr>
    <tr>
      <th>6</th>
      <td>&gt;34</td>
      <td>57</td>
      <td>3.080175</td>
      <td>175.57</td>
      <td>175.57</td>
      <td>3.080175</td>
    </tr>
  </tbody>
</table>
</div>




```python
purch_top_df = pd.DataFrame(purch_df.groupby(['SN'], as_index=False)
                           .agg({'Item Name':'count', 'Price':[np.mean,np.sum]}))
purch_top_df.columns = purch_top_df.columns.droplevel(1)
purch_top_df.columns = ['Player','Count', 'Mean', 'Sum']

purch_top_df.sort_values('Sum',ascending=False).head(5)

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Player</th>
      <th>Count</th>
      <th>Mean</th>
      <th>Sum</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>22</th>
      <td>Aerithllora36</td>
      <td>5</td>
      <td>3.950</td>
      <td>19.75</td>
    </tr>
    <tr>
      <th>574</th>
      <td>Undirrala66</td>
      <td>5</td>
      <td>3.412</td>
      <td>17.06</td>
    </tr>
    <tr>
      <th>503</th>
      <td>Sondim43</td>
      <td>5</td>
      <td>3.332</td>
      <td>16.66</td>
    </tr>
    <tr>
      <th>515</th>
      <td>Sundaky74</td>
      <td>4</td>
      <td>3.705</td>
      <td>14.82</td>
    </tr>
    <tr>
      <th>360</th>
      <td>Lisosiast26</td>
      <td>4</td>
      <td>3.475</td>
      <td>13.90</td>
    </tr>
  </tbody>
</table>
</div>




```python
purch_topitem_df = pd.DataFrame(purch_df.groupby(['Item Name'], as_index=False)
                           .agg({'SN':'count', 'Price':[np.mean,np.sum]}))
purch_topitem_df.columns = purch_topitem_df.columns.droplevel(1)
purch_topitem_df.columns = ['Player','Count', 'Mean', 'Sum']
purch_topitem_df = purch_topitem_df.sort_values('Count',ascending=False).head(5)
purch_topitem_df.reset_index(level=0, inplace=True)
purch_topitem_df.rename(columns={'index':'Item ID'})
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item ID</th>
      <th>Player</th>
      <th>Count</th>
      <th>Mean</th>
      <th>Sum</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>56</td>
      <td>Final Critic</td>
      <td>14</td>
      <td>2.757143</td>
      <td>38.60</td>
    </tr>
    <tr>
      <th>1</th>
      <td>8</td>
      <td>Arcane Gem</td>
      <td>12</td>
      <td>2.445000</td>
      <td>29.34</td>
    </tr>
    <tr>
      <th>2</th>
      <td>138</td>
      <td>Stormcaller</td>
      <td>12</td>
      <td>3.349167</td>
      <td>40.19</td>
    </tr>
    <tr>
      <th>3</th>
      <td>11</td>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>11</td>
      <td>2.350000</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>4</th>
      <td>156</td>
      <td>Trickster</td>
      <td>10</td>
      <td>2.322000</td>
      <td>23.22</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Most profitable item
purch_topitem_df = pd.DataFrame(purch_df.groupby(['Item Name'], as_index=False)
                           .agg({'SN':'count', 'Price':[np.mean,np.sum]}))
purch_topitem_df.columns = purch_topitem_df.columns.droplevel(1)
purch_topitem_df.columns = ['Player','Count', 'Mean', 'Sum']
purch_topitem_df = purch_topitem_df.sort_values('Sum',ascending=False).head(5)
purch_topitem_df.reset_index(level=0, inplace=True)
purch_topitem_df.rename(columns={'index':'Item ID'})
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item ID</th>
      <th>Player</th>
      <th>Count</th>
      <th>Mean</th>
      <th>Sum</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>138</td>
      <td>Stormcaller</td>
      <td>12</td>
      <td>3.349167</td>
      <td>40.19</td>
    </tr>
    <tr>
      <th>1</th>
      <td>56</td>
      <td>Final Critic</td>
      <td>14</td>
      <td>2.757143</td>
      <td>38.60</td>
    </tr>
    <tr>
      <th>2</th>
      <td>113</td>
      <td>Retribution Axe</td>
      <td>9</td>
      <td>4.140000</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>3</th>
      <td>135</td>
      <td>Splitter, Foe Of Subtlety</td>
      <td>9</td>
      <td>3.670000</td>
      <td>33.03</td>
    </tr>
    <tr>
      <th>4</th>
      <td>133</td>
      <td>Spectral Diamond Doomblade</td>
      <td>7</td>
      <td>4.250000</td>
      <td>29.75</td>
    </tr>
  </tbody>
</table>
</div>


