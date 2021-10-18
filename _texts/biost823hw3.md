---
layout: post
title: "BIOST823 HW3 - Tidy Tuesday"
date: "2021-10-01"
author: Junrong Lin
tag: 
---
To practice making plots with [ploty](https://plotly.com/) in Python, I'm going to using [tidy tuesday](https://github.com/rfordatascience/tidytuesday/tree/master/data/2018/2018-11-13) dataset. It shows incidence rate and death rate across the world for all ages. The following content describe how I and make them into interactive graphs. Let's get started!

### **0. Setup and load data**  
Use packages:
```python
%matplotlib inline
import numpy as np
import pandas as pd
import seaborn as sns
import plotly.express as px
```
Read .csv files:
```python
malaria_inc_url = "https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2018/2018-11-13/malaria_inc.csv"
malaria_deaths_url = "https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2018/2018-11-13/malaria_deaths.csv"
malaria_deaths_age_url = "https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2018/2018-11-13/malaria_deaths_age.csv"
df_inc = pd.read_csv(malaria_inc_url)
df_death = pd.read_csv(malaria_deaths_url)
df_deage = pd.read_csv(malaria_deaths_age_url)
df_deage = df_deage.drop(df_deage.columns[0], 1)
```
I choose to drop the first column containing index of raw in `malaria_deaths_age.csv`.  Next, let's have a glimpse of the data.

### **1. Quick look at data structure**  
```python
df_inc.head()
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
      <th>Entity</th>
      <th>Code</th>
      <th>Year</th>
      <th>Incidence of malaria (per 1,000 population at risk) (per 1,000 population at risk)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>2000</td>
      <td>107.1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>2005</td>
      <td>46.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>2010</td>
      <td>23.9</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>2015</td>
      <td>23.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Algeria</td>
      <td>DZA</td>
      <td>2000</td>
      <td>0.037746</td>
    </tr>
  </tbody>
</table>
</div>
```python
df_death.head()
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
      <th>Entity</th>
      <th>Code</th>
      <th>Year</th>
      <th>Deaths - Malaria - Sex: Both - Age: Age-standardized (Rate) (per 100,000 people)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>1990</td>
      <td>6.802930</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>1991</td>
      <td>6.973494</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>1992</td>
      <td>6.989882</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>1993</td>
      <td>7.088983</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>1994</td>
      <td>7.392472</td>
    </tr>
  </tbody>
</table>
</div>

```python
df_deage.head()
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
      <th>entity</th>
      <th>code</th>
      <th>year</th>
      <th>age_group</th>
      <th>deaths</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>1990</td>
      <td>Under 5</td>
      <td>184.606435</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>1991</td>
      <td>Under 5</td>
      <td>191.658193</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>1992</td>
      <td>Under 5</td>
      <td>197.140197</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>1993</td>
      <td>Under 5</td>
      <td>207.357753</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>1994</td>
      <td>Under 5</td>
      <td>226.209363</td>
    </tr>
  </tbody>
</table>
</div>

### **2. Visualize malaria incidence rate across over the world**  
To realize an interactive world map, I choose to use `choropleth()` function in `plotly.express` package.  One of my interactive map is shown below, the intensity of incidence rate is by visualized by a red color map. Users can use the slider to choose the year of interest and hover over country to see detailed statistics. All of these can be magically generated in several lines of code via plotly!
```python
fig = px.choropleth(df_inc,    
                       locations = "Code",
                       color="Incidence of malaria (per 1,000 population at risk) (per 1,000 population at risk)", 
                       hover_name="Entity", 
                       animation_frame="Year",
                       color_continuous_scale="Reds",
                       range_color=[0, 1000],
                       title='Incidence of malaria 2000-2015')
fig.layout.coloraxis.colorbar.title = "Incidence of malaria<br>(per 1,000 population at risk)"
fig.show()
```
{% include_relative malaria_incidence_rate.html %}

### **3. Animation death trend over 1990-2016**  
We are also curious about death incidences over different age group and our effort to eliminate this disease in the past decades. Nothing could be better than a animation of death cases in different age groups over years. Plotly also provide an animated bar plot `bar()` function in `plotly.express` package. Just by specifying `animation_frame` parameter it can automatically generate a bar plot animation.
```python
fig = px.bar(df_deage, x="age_group", y="deaths", color="age_group",
  animation_frame="year", animation_group="entity", range_y=[0,4000000])
fig.show()
```
