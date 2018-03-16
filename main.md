

```python
##OBSERVED TREND 1: This game is primarily played by males, and nearly half of the game's users are in their early 20s.

##OBSERVED TREND 2: With 780 total purchases, and 573 players that made a purchase, the vast majority of players made exactly one purchase.

##OBSERVED TREND 3: Players who make multiple purchases seem to favor the cheaper ones

```


```python
import pandas as pd
import json
from sklearn import preprocessing
```


```python
data=json.load(open('purchase_data.json'))
dataframe=pd.DataFrame(data)
```


```python
df_minus_duplicate_names = dataframe.drop_duplicates(subset=["SN"])
```


```python
#Total Number of Unique Players
totalplayers = dataframe["SN"].nunique()
#Number of Unique Items
uniqueitems = dataframe["Item ID"].nunique()
#Total Revenue
revenue = dataframe["Price"].sum()
#Total Number of Purchases
purchases = len(dataframe)
#Average Purchase Price
avgpurchaseprice = round(revenue/purchases,2)
overview = {"Total Players":totalplayers,"Unique Items":uniqueitems,"Revenue":revenue,"Purchases":purchases,"Average Purchase Price":avgpurchaseprice}
overview_df = pd.Series(overview).to_frame()
overview_df.columns = [" "]
overview_df
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Average Purchase Price</th>
      <td>2.93</td>
    </tr>
    <tr>
      <th>Purchases</th>
      <td>780.00</td>
    </tr>
    <tr>
      <th>Revenue</th>
      <td>2286.33</td>
    </tr>
    <tr>
      <th>Total Players</th>
      <td>573.00</td>
    </tr>
    <tr>
      <th>Unique Items</th>
      <td>183.00</td>
    </tr>
  </tbody>
</table>
</div>




```python
#How many males, females, other?
df_male = dataframe.loc[dataframe["Gender"]=="Male",:]
df_male_minus_duplicates = df_male.drop_duplicates(subset=["SN"])
df_female = dataframe.loc[dataframe["Gender"]=="Female",:]
df_female_minus_duplicates = df_female.drop_duplicates(subset=["SN"])
df_other = dataframe.loc[~dataframe["Gender"].isin(["Male","Female"]),:]
df_other_minus_duplicates = df_other.drop_duplicates(subset=["SN"])
```


```python
#numbers of each gender
gender_breakdown = {"Males":len(df_male_minus_duplicates), "Females":len(df_female_minus_duplicates), "Others":len(df_other_minus_duplicates)}
gender_df = pd.Series(gender_breakdown).to_frame()
gender_df["% of players"] = [len(df_male_minus_duplicates)/totalplayers*100,len(df_female_minus_duplicates)/totalplayers*100,len(df_other_minus_duplicates)/totalplayers*100]
gender_df.columns=["Player Count","% of all players"]
gender_df
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
      <th>Player Count</th>
      <th>% of all players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Females</th>
      <td>100</td>
      <td>81.151832</td>
    </tr>
    <tr>
      <th>Males</th>
      <td>465</td>
      <td>17.452007</td>
    </tr>
    <tr>
      <th>Others</th>
      <td>8</td>
      <td>1.396161</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Purchase Count by gender
purchase_count_m = len(df_male)
purchase_count_f = len(df_female)
purchase_count_o = len(df_other)
#Average Purchase Price by gender
avg_price_m = round(df_male["Price"].mean(),2)
avg_price_f = round(df_female["Price"].mean(),2)
avg_price_o = round(df_other["Price"].mean(),2)
#Total Purchase Value by gender
tot_price_m = round(df_male["Price"].sum(),2)
tot_price_f = round(df_female["Price"].sum(),2)
tot_price_o = round(df_other["Price"].sum(),2)

genderlist = ["Male","Female","Other"]
genderpcount = [purchase_count_m,purchase_count_f,purchase_count_o]
genderpprice = [avg_price_m,avg_price_f,avg_price_o]
gendertprice = [tot_price_m,tot_price_f,tot_price_o]

totpricedf = pd.DataFrame({"Gender":genderlist})
totpricedf.set_index("Gender")
totpricedf["Purchase Count"]=genderpcount
totpricedf["Average Purchase"]=genderpprice
totpricedf["Total Purchase Value"] = gendertprice
totpricedf["Normalized Totals"]=""

x = totpricedf[["Total Purchase Value"]].values.astype(float)
scaler=preprocessing.MinMaxScaler()
xscale=scaler.fit_transform(x)
totpricedf["Normalized Totals"]=pd.DataFrame(xscale)
totpricedf.set_index(["Gender"])
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
      <th>Average Purchase</th>
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
      <td>2.95</td>
      <td>1867.68</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>2.82</td>
      <td>382.91</td>
      <td>0.189509</td>
    </tr>
    <tr>
      <th>Other</th>
      <td>11</td>
      <td>3.25</td>
      <td>35.74</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#breaking down age groups by bins:
#(0-9) (10-14) (15-19) (20-24) ......
bins=[0,10,15,20,25,30,35,40,2000]
labels=['00-09','10-14','15-19','20-24','25-29','30-34','35-39','40+']
agegroups = pd.cut(dataframe["Age"],bins,labels=labels,include_lowest=True,retbins=True,right=False)
dataframe["AgeGroups"]=agegroups[0]

```


```python

df00to09 = dataframe.loc[dataframe["AgeGroups"]=="00-09",:]
df10to14 = dataframe.loc[dataframe["AgeGroups"]=="10-14",:]
df15to19 = dataframe.loc[dataframe["AgeGroups"]=="15-19",:]
df20to24 = dataframe.loc[dataframe["AgeGroups"]=="20-24",:]
df25to29 = dataframe.loc[dataframe["AgeGroups"]=="25-29",:]
df30to34 = dataframe.loc[dataframe["AgeGroups"]=="30-34",:]
df35to39 = dataframe.loc[dataframe["AgeGroups"]=="35-39",:]
df40up = dataframe.loc[dataframe["AgeGroups"]=="40+",:]

#Purchase Count by age group
pcount00to09 = len(df00to09)
pcount10to14 = len(df10to14)
pcount15to19 = len(df15to19)
pcount20to24 = len(df20to24)
pcount25to29 = len(df25to29)
pcount30to34 = len(df30to34)
pcount35to39 = len(df35to39)
pcount40up = len(df40up)

#Average Purchase Price by gender
avgprice00to09 = round(df00to09["Price"].mean(),2)
avgprice10to14 = round(df10to14["Price"].mean(),2)
avgprice15to19 = round(df15to19["Price"].mean(),2)
avgprice20to24 = round(df20to24["Price"].mean(),2)
avgprice25to29 = round(df25to29["Price"].mean(),2)
avgprice30to34 = round(df30to34["Price"].mean(),2)
avgprice35to39 = round(df35to39["Price"].mean(),2)
avgprice40up = round(df40up["Price"].mean(),2)
pprice = pd.DataFrame([avgprice00to09,avgprice10to14,avgprice15to19,avgprice20to24,avgprice25to29,avgprice30to34,avgprice35to39,avgprice40up])

#Total Purchase Value by gender
totprice00to09 = round(df00to09["Price"].sum(),2)
totprice10to14 = round(df10to14["Price"].sum(),2)
totprice15to19 = round(df15to19["Price"].sum(),2)
totprice20to24 = round(df20to24["Price"].sum(),2)
totprice25to29 = round(df25to29["Price"].sum(),2)
totprice30to34 = round(df30to34["Price"].sum(),2)
totprice35to39 = round(df35to39["Price"].sum(),2)
totprice40up = round(df40up["Price"].sum(),2)

tprice = pd.DataFrame([totprice00to09,totprice10to14,totprice15to19,totprice20to24,totprice25to29,totprice30to34,totprice35to39,totprice40up])
```


```python
agegroupdata = pd.DataFrame(dataframe["AgeGroups"].value_counts()).reset_index().sort_values("index",ascending=True)
agegroupdata.columns = ["AgeGroups","Purchase Count"]
agegroupdata["Average Purchase Price"] = pprice
agegroupdata["Total Purchase Price"] = agegroupdata["Average Purchase Price"] * agegroupdata["Purchase Count"]

x = agegroupdata[["Total Purchase Price"]].values.astype(float)
scaler=preprocessing.MinMaxScaler()
xscale=scaler.fit_transform(x)
agegroupdata["Normalized Totals"]=pd.DataFrame(xscale)
agegroupdata.set_index("AgeGroups")
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
      <th>Total Purchase Price</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>AgeGroups</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>00-09</th>
      <td>28</td>
      <td>2.84</td>
      <td>79.52</td>
      <td>0.074507</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>3.08</td>
      <td>107.80</td>
      <td>0.139854</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>2.77</td>
      <td>368.41</td>
      <td>0.057073</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>2.98</td>
      <td>1001.28</td>
      <td>0.027228</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>2.91</td>
      <td>363.75</td>
      <td>0.332106</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>2.91</td>
      <td>186.24</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>2.96</td>
      <td>124.32</td>
      <td>0.327188</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>3.16</td>
      <td>53.72</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Age breakdown
df_minus_duplicate_names = dataframe.drop_duplicates(subset=["SN"])
output_age_groups = pd.DataFrame(df_minus_duplicate_names["AgeGroups"].value_counts()).reset_index().sort_values("index").set_index("index")
output_age_groups.columns = ["Count"]
output_age_groups["% of players"] = output_age_groups["Count"]/totalplayers*100
output_age_groups #OUTPUT

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
      <th>Count</th>
      <th>% of players</th>
    </tr>
    <tr>
      <th>index</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>00-09</th>
      <td>19</td>
      <td>3.315881</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>23</td>
      <td>4.013962</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>100</td>
      <td>17.452007</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>259</td>
      <td>45.200698</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>87</td>
      <td>15.183246</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>47</td>
      <td>8.202443</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>27</td>
      <td>4.712042</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>11</td>
      <td>1.919721</td>
    </tr>
  </tbody>
</table>
</div>




```python
#TOP SPENDERS
#* Identify the the top 5 spenders in the game by total purchase value, then list (in a table):
#  * SN
#  * Purchase Count
#  * Average Purchase Price
#  * Total Purchase Value


groupeddf = dataframe.groupby(["SN"]).Price.sum().reset_index()
sortedspenders = groupeddf.sort_values("Price", ascending=False).head()
top5spendersnames = sortedspenders["SN"].values.tolist()
top5spendersdf = dataframe[dataframe["SN"].isin(top5spendersnames)]
top5spendersdf.groupby(["SN"]).Price.sum()
top5purchasecount = top5spendersdf["SN"].value_counts().reset_index()#top5purchasecount
top5totalvalue = top5spendersdf.groupby(["SN"]).Price.sum().reset_index()
sortedtop5totalvalue = top5totalvalue.sort_values("Price", ascending=False) #top5totalvalue
topspendersoutputdf = pd.DataFrame({"SN": top5spendersnames})
topspendersoutputdf["Purchase Count"] = top5purchasecount["SN"]
output_top_spenders = pd.merge(topspendersoutputdf,sortedtop5totalvalue,on="SN")
output_top_spenders["Average Purchase Price"] = round(output_top_spenders["Price"]/output_top_spenders["Purchase Count"],2)
output_top_spenders.set_index(["SN"])
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
      <th>Price</th>
      <th>Average Purchase Price</th>
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
      <td>17.06</td>
      <td>3.41</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>13.56</td>
      <td>3.39</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>12.74</td>
      <td>3.18</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>12.73</td>
      <td>4.24</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>11.58</td>
      <td>3.86</td>
    </tr>
  </tbody>
</table>
</div>




```python
itemcounts = dataframe["Item ID"].value_counts().reset_index()
itemcounts.columns= ["Item ID","Count"]
```


```python
#**Most Popular Items**

#* Identify the 5 most popular items by purchase count, then list (in a table):
#  * Item ID
#  * Item Name
#  * Purchase Count
#  * Item Price
#  * Total Purchase Value

itemgroup = dataframe.groupby(["Item Name","Item ID", "Price"]).count().reset_index()
sorteditemgroup = itemgroup.sort_values("SN",ascending=False).head()
top5df = dataframe[dataframe["Item ID"].isin(sorteditemgroup["Item ID"])]
top5itemsdf= top5df["Item ID"].value_counts().reset_index()
top5itemsdf.columns=["Item ID","Count"]
grouptop5df = top5df.groupby(["Item ID"]).Price.sum().reset_index().sort_values("Price",ascending=False)
grouptop5df.columns=["Item ID","Total Purchase Value"]
outputpart1 = sorteditemgroup[["Item ID","Item Name","Price"]]
outputpart2 = pd.merge(outputpart1,top5itemsdf,on="Item ID")
output_most_popular = pd.merge(outputpart2,grouptop5df,on="Item ID")
output_most_popular.set_index(["Item ID"]) #FINAL RESULT
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
      <th>Item Name</th>
      <th>Price</th>
      <th>Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>2.35</td>
      <td>11</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Arcane Gem</td>
      <td>2.23</td>
      <td>11</td>
      <td>24.53</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Retribution Axe</td>
      <td>4.14</td>
      <td>9</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Trickster</td>
      <td>2.07</td>
      <td>9</td>
      <td>18.63</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Serenity</td>
      <td>1.49</td>
      <td>9</td>
      <td>13.41</td>
    </tr>
  </tbody>
</table>
</div>




```python

#* Identify the 5 most profitable items by total purchase value, then list (in a table):
#  * Item ID
#  * Item Name
#  * Purchase Count
#  * Item Price
#  * Total Purchase Value

itemprofit=dataframe.groupby(["Item ID","Item Name","Price"]).Price.sum()
profitdf=pd.DataFrame(itemprofit)
profitdf.columns=["Total Purchase Value"]
profitdf = profitdf.reset_index()
sortprofit=profitdf.sort_values("Total Purchase Value",ascending=False).head()
output_most_profit = pd.merge(sortprofit,itemcounts,on="Item ID",how="inner")
output_most_profit.set_index(["Item ID"]) #FINAL RESULT
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
      <th>Item Name</th>
      <th>Price</th>
      <th>Total Purchase Value</th>
      <th>Count</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <td>Retribution Axe</td>
      <td>4.14</td>
      <td>37.26</td>
      <td>9</td>
    </tr>
    <tr>
      <th>115</th>
      <td>Spectral Diamond Doomblade</td>
      <td>4.25</td>
      <td>29.75</td>
      <td>7</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Orenmir</td>
      <td>4.95</td>
      <td>29.70</td>
      <td>6</td>
    </tr>
    <tr>
      <th>103</th>
      <td>Singed Scalpel</td>
      <td>4.87</td>
      <td>29.22</td>
      <td>6</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>3.61</td>
      <td>28.88</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>


