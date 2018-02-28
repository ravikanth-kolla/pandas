
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
<table style="width:100%">
<caption>Observable trends</caption>
  <tr>
    <th>Serial #</th>
    <th>Observation</th> 
  </tr>
  <tr>
    <td>1</td>
    <td>Purchasers are overwhelmingly male.</td> 
  </tr>
  <tr>
    <td>2</td>
    <td>Maximum purchases by 20 -24 age group.</td> 
  </tr>
  <tr>
    <td>3</td>
    <td>The highest average spending is from the 40+ age group.</td> 
  </tr>
</table>
</div>



```python
import pandas as pd
import numpy as np
```


```python
# Create a path to the csv and read it into a Pandas DataFrame
#Change this to other JSON file if you want to read from other JSON files
#Assumptions is that files represent separate sets of data and must be analyzed separately
json_path = "Resources/purchase_data.json"
pymoli_df = pd.read_json(json_path)

#Check file was read correctly
pymoli_df.head()

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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>




```python
#<b>Player Count</b>
pymoli_unique_players_count = len(pymoli_df["SN"].unique())

pymoli_unique_players_df = pd.DataFrame({"Total Players":[pymoli_unique_players_count]})
pymoli_unique_players_df
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
#<b>Purchasing Analysis</b>

pymoli_unique_items_count = len(pymoli_df["Item ID"].unique())

pymoli_avg_price = '${:,.2f}'.format(round(pymoli_df["Price"].mean(),2))

pymoli_Num_purchases = len(pymoli_df["Item ID"])

pymoli_total_revenue = '${:,.2f}'.format(round(pymoli_df["Price"].sum(),2))

#Create a data frame to hold the purchasing analysis from values above
purchasing_analysis_df = pd.DataFrame([[pymoli_unique_items_count,pymoli_avg_price,pymoli_Num_purchases,pymoli_total_revenue]], \
                                     columns=["Number of Unique Items","Average Price","Number of Purchases","Total Revenue"])
purchasing_analysis_df
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
#<b>Gender Demographics</b>

#####
#Observation 1:  Purchasers are overwhelmingly male
#####

pymoli_groupby_sn_gender = pymoli_df.groupby(["SN","Gender"])
pymoli_unique_payers_df = pd.DataFrame(pymoli_groupby_sn_gender.size())
#print(pymoli_unique_payers_df)
pymoli_gender_agg_df = pd.DataFrame(pymoli_unique_payers_df.groupby(["Gender"]).count())
pymoli_gender_agg_df.columns= ["Total Count"]
#print(pymoli_gender_agg_df)
pymoli_gender_agg_df["Percentage of Players"] = round(100*(pymoli_gender_agg_df["Total Count"]/pymoli_gender_agg_df["Total Count"].sum()),2)

#Rearrange as shown in sample
pymoli_gender_agg_df= pymoli_gender_agg_df[["Percentage of Players","Total Count"]]


##Need these values for normalized totals
female_count = pymoli_gender_agg_df["Total Count"].loc["Female"]
#print(female_count)
male_count = pymoli_gender_agg_df["Total Count"].loc["Male"]
#print(male_count)
other_count = pymoli_gender_agg_df["Total Count"].loc["Other / Non-Disclosed"]
#print(other_count)

pymoli_gender_agg_df
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
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
#<b>Gender Purchase Analysis</b>
#The below each broken by gender
#Purchase Count
#Average Purchase Price
#Total Purchase Value
#Normalized Totals


male_purchases = len(pymoli_df[pymoli_df["Gender"] == "Male"])
#print(male_purchases)
male_purchase_total = '${:,.2f}'.format(round(pymoli_df[pymoli_df["Gender"] == "Male"]["Price"].sum(),2))
#print(male_purchase_total)
male_purchase_avg = '${:,.2f}'.format(round(pymoli_df[pymoli_df["Gender"] == "Male"]["Price"].mean(),2))
#print(male_purchase_avg)
male_norm_tot = '${:,.2f}'.format(round(pymoli_df[pymoli_df["Gender"] == "Male"]["Price"].sum(),2)/male_count)
#print(male_norm_tot)

female_purchases = len(pymoli_df[pymoli_df["Gender"] == "Female"])
#print(female_purchases)
female_purchase_total = '${:,.2f}'.format(round(pymoli_df[pymoli_df["Gender"] == "Female"]["Price"].sum(),2))
#print(female_purchase_total)
female_purchase_avg = '${:,.2f}'.format(round(pymoli_df[pymoli_df["Gender"] == "Female"]["Price"].mean(),2))
#print(female_purchase_avg)
female_norm_tot = '${:,.2f}'.format(round(pymoli_df[pymoli_df["Gender"] == "Female"]["Price"].sum(),2)/female_count)
#print(female_norm_tot)

other_purchases = len(pymoli_df[pymoli_df["Gender"] == "Other / Non-Disclosed"])
#print(other_purchases)
other_purchase_total = '${:,.2f}'.format(round(pymoli_df[pymoli_df["Gender"] == "Other / Non-Disclosed"]["Price"].sum(),2))
#print(other_purchase_total)
other_purchase_avg = '${:,.2f}'.format(round(pymoli_df[pymoli_df["Gender"] == "Other / Non-Disclosed"]["Price"].mean(),2))
#print(other_purchase_avg)
other_norm_tot = '${:,.2f}'.format(round(pymoli_df[pymoli_df["Gender"] == "Other / Non-Disclosed"]["Price"].sum(),2)/other_count)
#print(other_norm_tot)

gender_purchase_analysis_df = pd.DataFrame({"Gender":["Male","Female","Other / Non-Disclosed"],
                                           "Purchase Count":[male_purchases,female_purchases,other_purchases],
                                           "Average Purchase Price":[male_purchase_avg,female_purchase_avg,other_purchase_avg],
                                           "Total Purchase Value":[male_purchase_total,female_purchase_total,other_purchase_total],
                                           "Normalized Totals":[male_norm_tot,female_norm_tot,other_norm_tot]},
                                          columns =["Gender","Purchase Count","Average Purchase Price","Total Purchase Value","Normalized Totals"])

gender_purchase_analysis_df.set_index("Gender", inplace=True) 

gender_purchase_analysis_df
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
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1,867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>




```python
#<b>Age Demographics</b>

#####
#Observation 2:  Maximum purchases by 20 -24 age grp
#####

#The below each broken into bins of 4 years (i.e. <10, 10-14, 15-19, etc.)


# Create the bins in which Data will be held
# Bins are 0 to 9, 10 to 14, 15 to 19, 20 to 24... to 40+ Use 100 as max for 40+
bins = [0, 9, 14, 19, 24,29,34,39,100]

# Create the names for the age bins
group_names = ['<10', '10-14', '15-19', '20-24','25-29','30-34','35-39','40+']


pd.cut(pymoli_df["Age"], bins, labels=group_names)
pymoli_df["Age Group"] = pd.cut(pymoli_df["Age"], bins, labels=group_names)

pymoli_groupby_sn_agegrp = pymoli_df.groupby(["SN","Age Group"])

                                                              
pymoli_unique_payers_df = pd.DataFrame(pymoli_groupby_sn_agegrp.size())
#print(pymoli_unique_payers_df)
pymoli_agegrp_agg_df = pd.DataFrame(pymoli_unique_payers_df.groupby(["Age Group"]).count())

pymoli_agegrp_agg_df.columns= ["Total Count"]
pymoli_agegrp_agg_df["Percentage of Players"] = round(100 * \
                                                pymoli_agegrp_agg_df["Total Count"]/ pymoli_agegrp_agg_df["Total Count"].sum(),2)
pymoli_agegrp_agg_df
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
      <th>Total Count</th>
      <th>Percentage of Players</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>19</td>
      <td>3.32</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>23</td>
      <td>4.01</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>100</td>
      <td>17.45</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>259</td>
      <td>45.20</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>87</td>
      <td>15.18</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>47</td>
      <td>8.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>27</td>
      <td>4.71</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>11</td>
      <td>1.92</td>
    </tr>
  </tbody>
</table>
</div>




```python
#####
#Observation 3:  The highest average spending is from the group 40+ in age
#####

##<b>Purchasing Analysis by Age</b>
#Purchase Count
#Average Purchase Price
#Total Purchase Value
#Normalized Totals

#group by just age group to get counts by age group. Index is still "Age Group"
pymoli_agegrp_agg_not_sn_df = pd.DataFrame(pymoli_df.groupby(["Age Group"]).count())

#group by just age group to get sum by age group. Index is still "Age Group"
pymoli_agegrp_sum_not_sn_df = pd.DataFrame(pymoli_df.groupby(["Age Group"]).sum())

pymoli_agegrp_agg_df["Purchase Count"] =  pymoli_agegrp_agg_not_sn_df["Item ID"]

pymoli_agegrp_agg_df["Average Purchase Price"] = round(pymoli_agegrp_sum_not_sn_df["Price"]/ \
                                                    pymoli_agegrp_agg_df["Purchase Count"],2).map("${:,.2f}".format)
pymoli_agegrp_agg_df["Total Purchase Value"] = pymoli_agegrp_sum_not_sn_df["Price"]

pymoli_agegrp_agg_df["Normalized Totals"] =  round(pymoli_agegrp_agg_df["Total Purchase Value"]/ \
                                                    pymoli_agegrp_agg_df["Total Count"],2).map("${:,.2f}".format)

pymoli_agegrp_agg_df["Total Purchase Value"] = pymoli_agegrp_agg_df["Total Purchase Value"].map("${:,.2f}".format)

##Remove columns we don't want to show
pymoli_agegrp_agg_df = pymoli_agegrp_agg_df.drop(labels = ["Total Count","Percentage of Players"],axis = 1)

pymoli_agegrp_agg_df
                                


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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Group</th>
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
      <td>$4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
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
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
  </tbody>
</table>
</div>




```python
##<b>Top Spenders</b>

##Identify the the top 5 spenders in the game by total purchase value, then list (in a table):
##SN
##Purchase Count
##Average Purchase Price
##Total Purchase Value

#Get counts of purchases
pymoli_counts_df = pd.DataFrame(pymoli_df.groupby("SN").count())

#Get sum of prices
pymoli_sum_df = pd.DataFrame(pymoli_df.groupby("SN").sum())

#start with sum of prices and add columns alomng the way
pymoli_spenders_df = pymoli_sum_df

#Before that Sort by total purchase price descending
pymoli_spenders_df = pymoli_spenders_df.sort_values(by=["Price"], ascending=False)

#Add remaining columns
pymoli_spenders_df["Purchase Count"] = pymoli_counts_df["Item ID"]
pymoli_spenders_df["Average Purchase Price"] = round(pymoli_spenders_df["Price"]/pymoli_spenders_df["Purchase Count"],2).map("${:,.2f}".format)
pymoli_spenders_df["Total Purchase Value"] = pymoli_spenders_df["Price"].map("${:,.2f}".format)

#Drop the unnecessary columns
pymoli_spenders_df = pymoli_spenders_df.drop(labels = ["Age","Item ID","Price"],axis = 1)

#Show the top 5
pymoli_spenders_df.head(5)
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
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
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>




```python
#<b>Most Popular Items</b>

#Identify the 5 most popular items by purchase count, then list (in a table):
#Item ID
#Item Name
#Purchase Count
#Item Price
#Total Purchase Value

#get the count after grouping by Item ID to get count of items sold
pymoli_items_count_df = pd.DataFrame(pymoli_df.groupby(["Item ID","Item Name"]).count())

#get the sum after grouping by Item ID to find total sales of item
pymoli_items_sum_df = pd.DataFrame(pymoli_df.groupby(["Item ID","Item Name"]).sum())

#Add the columns required
pymoli_items_count_df["Purchase Count"] = pymoli_items_count_df["Age"]
pymoli_items_count_df["Item Price"] = round(pymoli_items_sum_df["Price"]/pymoli_items_count_df["Age"],2)
pymoli_items_count_df["Total Purchase Value"] = pymoli_items_sum_df["Price"]

#Sort by count descending to get most popular on top and Total Purchase Value as secondary critera in case of same item count
pymoli_items_count_df = pymoli_items_count_df.sort_values(by = ["Purchase Count","Total Purchase Value"],ascending = False)

#Get these into the new dataframe with unnecessary columns removed
pymoli_popular_items_df = pymoli_items_count_df.drop(labels = ["Age","Age Group","Gender","Price","SN"],axis = 1)

##Format the columns for currency
pymoli_popular_items_df["Item Price"] = pymoli_popular_items_df["Item Price"].map("${:,.2f}".format)
pymoli_popular_items_df["Total Purchase Value"] = pymoli_popular_items_df["Total Purchase Value"].map("${:,.2f}".format)


#List the top 5 
pymoli_popular_items_df.head(5)
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
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
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
#<b>Identify the 5 most profitable items by total purchase value, then list (in a table):</b>
#Item ID
#Item Name
#Purchase Count
#Item Price
#Total Purchase Value

#This can be done almost exactly as above except sorting on Price

#Sort by count descending to get most popular on top and Total Purchase Value as secondary critera in case of same item count
pymoli_items_count_df = pymoli_items_count_df.sort_values(by = ["Total Purchase Value"],ascending = False)

#Get these into the new dataframe with unnecessary columns removed
pymoli_profitable_items_df = pymoli_items_count_df.drop(labels = ["Age","Age Group","Gender","Price","SN"],axis = 1)

##Format the columns for currency
pymoli_profitable_items_df["Item Price"] = pymoli_profitable_items_df["Item Price"].map("${:,.2f}".format)
pymoli_profitable_items_df["Total Purchase Value"] = pymoli_profitable_items_df["Total Purchase Value"].map("${:,.2f}".format)


#List the top 5
pymoli_profitable_items_df.head(5)
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


