

```python
import pandas as pd
import json

```


```python
data=json.load(open('purchase_data.json'))
dataframe=pd.DataFrame(data)
```


```python
df_minus_duplicate_names = dataframe.drop_duplicates(subset=["SN"])
df_minus_duplicate_names.head()
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
#TOTAL NUMBER OF PLAYERS
totalplayers = dataframe["SN"].nunique()
totalplayers
```




    573




```python
#Number of Unique Items
uniqueitems = dataframe["Item ID"].nunique()
uniqueitems
```




    183




```python
#Total Revenue
revenue = dataframe["Price"].sum()
revenue
```




    2286.33




```python
#Total Number of Purchases
purchases = len(dataframe)
purchases
```




    780




```python
#Average Purchase Price
avgpurchaseprice = round(revenue/purchases,2)
avgpurchaseprice
```




    2.93




```python

```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-10-009b01fae4db> in <module>()
    ----> 1 outdf = pd.DataFrame({".":""})
          2 outdf
    

    ~\Anaconda3\envs\PythonData\lib\site-packages\pandas\core\frame.py in __init__(self, data, index, columns, dtype, copy)
        328                                  dtype=dtype, copy=copy)
        329         elif isinstance(data, dict):
    --> 330             mgr = self._init_dict(data, index, columns, dtype=dtype)
        331         elif isinstance(data, ma.MaskedArray):
        332             import numpy.ma.mrecords as mrecords
    

    ~\Anaconda3\envs\PythonData\lib\site-packages\pandas\core\frame.py in _init_dict(self, data, index, columns, dtype)
        459             arrays = [data[k] for k in keys]
        460 
    --> 461         return _arrays_to_mgr(arrays, data_names, index, columns, dtype=dtype)
        462 
        463     def _init_ndarray(self, values, index, columns, dtype=None, copy=False):
    

    ~\Anaconda3\envs\PythonData\lib\site-packages\pandas\core\frame.py in _arrays_to_mgr(arrays, arr_names, index, columns, dtype)
       6161     # figure out the index, if necessary
       6162     if index is None:
    -> 6163         index = extract_index(arrays)
       6164     else:
       6165         index = _ensure_index(index)
    

    ~\Anaconda3\envs\PythonData\lib\site-packages\pandas\core\frame.py in extract_index(data)
       6200 
       6201         if not indexes and not raw_lengths:
    -> 6202             raise ValueError('If using all scalar values, you must pass'
       6203                              ' an index')
       6204 
    

    ValueError: If using all scalar values, you must pass an index



```python
#How many males, females, other?
df_male = dataframe.loc[dataframe["Gender"]=="Male",:]
df_male_minus_duplicates = df_male.drop_duplicates(subset=["SN"])
df_female = dataframe.loc[dataframe["Gender"]=="Female",:]
df_female_minus_duplicates = df_female.drop_duplicates(subset=["SN"])
df_other = dataframe.loc[~dataframe["Gender"].isin(["Male","Female"]),:]# and dataframe["Gender"]!="Female",:]
df_other_minus_duplicates = df_other.drop_duplicates(subset=["SN"])
```


```python
#numbers of each gender
print("Males:", len(df_male_minus_duplicates), "\nFemales:", len(df_female_minus_duplicates), "\nOthers:", len(df_other_minus_duplicates))
```


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
#Normalized Totals by gender


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
totpricedf
```


```python
#breaking down age groups by bins:
#(0-9) (10-14) (15-19) (20-24) ......
bins=[0,10,15,20,25,30,35,40,2000]
labels=['00-09','10-14','15-19','20-24','25-29','30-34','35-39','40+']
agegroups = pd.cut(dataframe["Age"],bins,labels=labels,include_lowest=True,retbins=True,right=False)
dataframe["AgeGroups"]=agegroups[0]
dataframe.head()

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


agegroupdata = pd.DataFrame(dataframe["AgeGroups"].value_counts()).reset_index().sort_values("index",ascending=True)
agegroupdata= agegroupdata.drop("AgeGroups",1)
#agegroupdata.columns = ["AgeGroups","Purchase Count","Average Purchase Price","Total Purchase Price","Normalized Totals"]
agegroupdata
```


```python

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


#Total Purchase Value by gender
totprice00to09 = round(df00to09["Price"].sum(),2)
totprice10to14 = round(df10to14["Price"].sum(),2)
totprice15to19 = round(df15to19["Price"].sum(),2)
totprice20to24 = round(df20to24["Price"].sum(),2)
totprice25to29 = round(df25to29["Price"].sum(),2)
totprice30to34 = round(df30to34["Price"].sum(),2)
totprice35to39 = round(df35to39["Price"].sum(),2)
totprice40up = round(df40up["Price"].sum(),2)
#Normalized Totals by gender







```


```python
#Age breakdown
output_age_groups = pd.DataFrame(df_minus_duplicate_names["AgeGroups"].value_counts()).reset_index().sort_values("index").set_index("index")
output_age_groups.columns = ["Count"]
output_age_groups["% of players"] = output_age_groups["Count"]/totalplayers
output_age_groups #OUTPUT
```


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
output_top_spenders #FINAL RESULT
```


```python
itemcounts = dataframe["Item ID"].value_counts().reset_index()
itemcounts.columns= ["Item ID","Count"]
itemcounts.head()
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
output_most_popular #FINAL RESULT
```


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
output_most_profit #FINAL RESULT
```
