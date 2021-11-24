---
layout: post
title: "Using Plotly dash to build a simple dashboard"
date: "2021-10-25"
author: Junrong Lin
tag: 
---
This work is a practice to use [Plotly](https://plotly.com/) to build a simple interactive apps. Data is loaded from [NSF table 12](https://ncses.nsf.gov/pubs/nsf19301/data) ("Doctorate recipients, by major field of study: Selected years, 1987-2017")  

### **0. Setup and load data**  
Load packages
```python
import pandas as pd 
import numpy as np 
import seaborn as sns
import matplotlib.pyplot as plt
```  

Load data
```python
phd_df = pd.read_excel("sed17-sr-tab012.xlsx", skiprows=[0,1,2]).loc[:,["Field of study", 1987, 1992, 1997, 2002, 2007, 2012, 2017]]
phd_df = phd_df.drop([0]+[i for i in (range(41,46))])
phd_df.head()
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
<table border="1">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Field of study</th>
      <th>1987</th>
      <th>1992</th>
      <th>1997</th>
      <th>2002</th>
      <th>2007</th>
      <th>2012</th>
      <th>2017</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <th>All fields</th>
      <th>32365</th>
      <th>38886</th>
      <th>42539</th>
      <th>40031</th>
      <th>48132</th>
      <th>50944</th>
      <th>54664</th>
    </tr>
    <tr>
      <th>2</th>
      <th>Life sciences</th>
      <th>5783</th>
      <th>7172</th>
      <th>8421</th>
      <th>8478</th>
      <th>10702</th>
      <th>11964</th>
      <th>12592</th>
    </tr>
    <tr>
      <th>3</th>
      <th>Agricultural sciences and natural resources</th>
      <th>1144</th>
      <th>1261</th>
      <th>1212</th>
      <th>1129</th>
      <th>1321</th>
      <th>1255</th>
      <th>1606</th>
    </tr>
    <tr>
      <th>4</th>
      <th>Biological and biomedical sciences	</th>
      <th>3839</th>
      <th>4799</th>
      <th>5788</th>
      <th>5695</th>
      <th>7238</th>
      <th>8322</th>
      <th>8477</th>
    </tr>
    <tr>
      <th>5</th>
      <th>Health sciencs</th>
      <th>800</th>
      <th>1112</th>
      <th>1421</th>
      <th>1654</th>
      <th>2143</th>
      <th>2387</th>
      <th>2509</th>
    </tr>
  </tbody>
</table>
</div>

### **1. Define key mapping**  
Data has 2 Levels: Main fields-Subfields. Dashboard is defined to be able to show data of each Main fields. An overall option is also planned to provide. Since each field has subfields, to build line plots, we need to define a dictionary for the need of summation (e.g field "Mathematics and computer sciences has 2 subfields: Mathematics and statistics, Computer and information sciences"). A function `get_key()` is defined.
```python
phd_df["Main field"] = phd_df["Field of study"].apply(get_key)
phd_df.head()
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
<table border="1">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Field of study</th>
      <th>1987</th>
      <th>1992</th>
      <th>1997</th>
      <th>2002</th>
      <th>2007</th>
      <th>2012</th>
      <th>2017</th>
      <th>Main field</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <th>All fields</th>
      <th>32365</th>
      <th>38886</th>
      <th>42539</th>
      <th>40031</th>
      <th>48132</th>
      <th>50944</th>
      <th>54664</th>
      <th>All</th>
    </tr>
    <tr>
      <th>2</th>
      <th>Life sciences</th>
      <th>5783</th>
      <th>7172</th>
      <th>8421</th>
      <th>8478</th>
      <th>10702</th>
      <th>11964</th>
      <th>12592</th>
      <th>Main</th>
    </tr>
    <tr>
      <th>3</th>
      <th>Agricultural sciences and natural resources</th>
      <th>1144</th>
      <th>1261</th>
      <th>1212</th>
      <th>1129</th>
      <th>1321</th>
      <th>1255</th>
      <th>1606</th>
      <th>Life sciences</th>
    </tr>
    <tr>
      <th>4</th>
      <th>Biological and biomedical sciences	</th>
      <th>3839</th>
      <th>4799</th>
      <th>5788</th>
      <th>5695</th>
      <th>7238</th>
      <th>8322</th>
      <th>8477</th>
      <th>Life sciences</th>
    </tr>
    <tr>
      <th>5</th>
      <th>Health sciencs</th>
      <th>800</th>
      <th>1112</th>
      <th>1421</th>
      <th>1654</th>
      <th>2143</th>
      <th>2387</th>
      <th>2509</th>
      <th>Life sciences</th>
    </tr>
  </tbody>
</table>
</div>


### **2. build dash app**  
Callback function is the central theme for inteactive dash plot. It is implemented by specifying a pair of input and output  target figure and label with value.  
```python
@app.callback(
    Output('graph', 'figure'),
    Input("field", "value")
)
def update_figure(field):
    df = phd_df[phd_df["Main field"]==field].iloc[:,:-1]
    df = df.melt(id_vars=["Field of study"],
                 var_name="Year",
                 value_name="Number")
    fig = px.line(df, x="Year", y="Number", color="Field of study")
    return fig
# Run app and display result inline in the notebook
app.run_server(mode="external")
```
The final plot is like the following
![p085.png](/assets/biost823/dash_line.png)
