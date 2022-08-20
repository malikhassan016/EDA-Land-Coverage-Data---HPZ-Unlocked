# Team Entrepreneurs


### Step-8: Plotting the Data
#### **Visualization of World land coverage over the years(2001-2018)**
```pyhton
# Visualization of World land coverage over the years(2001-2018)

import plotly.express as px
fig = px.line(df1, x="Year",
              y="Value",
              labels={
                     "Value" : "Land Covered in Hectares (Ha)"
                 },
              color='Area',
              title='World Land Covered Areas')
fig.show()
```
![image](images/world-land.png)

The World Land Coverage plot shows the land coverage of world countries over the years. Over the years there is not much significant change in the land coverage. The world top land covered territories are Russian Federation, China, Antarctica, Canada,China, mainland.

#### **Land Coverage on a World Map**
```pyhton
# Create the layout of the chart
title = '<b>World Land Covered Areas</b>'
layout2 = go.Layout(title = {'text' : title, 
                            'x':0.5, 'xanchor': 'center'}, 
                   font = {"color" : 'black'},
                   width=980, height=600, plot_bgcolor="white", paper_bgcolor="white",
                   geo=dict(showframe=False, showcoastlines=False, projection_type='equirectangular'
                           )
                  )

# Create the figure
fig2 = go.Figure(layout = layout2)


# Create the Choropleth map tracing
trace2 = go.Choropleth(
    locations = df1['Area Code (ISO3)'],
    z = df1['Value'],
    text = df1['Area'],
    colorscale = 'Viridis',
    #color_continuous_scale="RdYlGn",
    autocolorscale=False,
    reversescale=True,
    marker_line_color='#2E2E2E',
    marker_line_width=0.5,
    colorbar_tickprefix = '%',
    colorbar_title = 'Landcover',
)

fig2.add_trace(trace2)
fig2.show()
```
![image](images/worldmap.png)
The world map shows the land coverage of the countries. The color bar shows the land coverage in hectares. The regions with purple color being the top land covered and the regions with the yellowish color being the least land covered.

#### **Top 10 Countries in world with maximum Tree covered Area**

```pyhton
s=Tree.groupby(["Area"]).sum().sort_values(by="Value", ascending=False).head(10)
plt.figure(figsize=(5,3), dpi=150, linewidth=2)
pl=sns.barplot(x='Value',y=s.index,data=s).set(title='Top 10 Countries in World with maximum Tree covered Area')
plt.xlabel("Land Covered in Hectares (Ha)")
```
![image](images/top10_tca.png)
Out of the top world countries Russian Federation have maximum land covered by Trees.


#### **Top 5 countries in world with snow and glaciers**

```pyhton
s=snow.groupby(["Area"]).sum().sort_values(by="Value", ascending=False).head(5)
plt.figure(figsize=(5,3), dpi=150, linewidth=2)
pl=sns.barplot(x='Value',y=s.index,data=s).set(title='Top 5 Countries in World with most snow and glaciers')
plt.xlabel("Land Covered in Hectares (Ha)")
```
![image](images/top5_sandg.png)
Out of the world top five countries/regions, Antarctica have maximum land covered by Snow and Glaciers.


#### **Tree Covered Areas of Top Land Covered Countries (2001 - 2018)**

```pyhton
fig = px.line(landcover, x="Year", y="Value",color='Area', 
                            labels={
                     "Value" : "Land Covered in Hectares (Ha)"
                 },
              title='Tree Covered Areas of Top Land Covered Countries (2001 - 2018')
fig.show()
```
![image](images/tca_top.png)


## Part-2: EDA on Subcontinent
Land Coverage from Subcontinent
In this section subcontinent countries will be examined. the list of countries are as follows:

* Bangladesh,
* India,
* Pakistan

### Feature Selection and Data Insights
```
# Selecting Subcontinents countries
df_selected = df_land[df_land["Area"].isin(["India", "Pakistan", "Bangladesh"])]
df_selected.head()
```
![image](images/south_Asia_table.png)

```
# Shape of selected subcontinents countries dataframe
df_selected.shape
```
> **OutPut:**
```
(540, 6)
```

```
# Summary Statistics of selected subcontinents countries dataframe
df_selected.describe()
```
> **OutPut:**

![image](images/southasia_summary.png)

#### Selecting the important features and combining them based on Land Coverage Value

```pyhton
dfsouth = df_selected.groupby(["Area", "Area Code (ISO3)", "Year"])["Value"].sum().reset_index()
dfsouth.head()
```
![image](images/dfsouth_head.png)

#### Checking the different Land Types in  the Subcontient

```python
df_selected["Item"].unique()
```
The different land types are:<br>
array(['Artificial surfaces (including urban and associated areas)',
       'Herbaceous crops', 'Grassland', 'Tree-covered areas', 'Mangroves',
       'Shrub-covered areas',
       'Shrubs and/or herbaceous vegetation, aquatic or regularly flooded',
       'Terrestrial barren land', 'Permanent snow and glaciers',
       'Inland water bodies'], dtype=object)

### **Visualizing Subcontinents Countries Land Coverage and Percent Changes Over the Time**

#### Subcontinent Countries Land Coverage over the years

```python
fig = px.line(dfsouth, x="Year", y="Value",
              labels={
                     "Value" : "Land Covered in Hectares (Ha)"
                 },
              color='Area', title='South Asia Land Covered Areas(2001 - 2018)')
fig.show()
```
![image](images/southasia_lca.png)

#### Land Coverage by Subcontinent Countries (Combine)

```python
dfsouth[dfsouth["Year"] == 2018].groupby('Area').Value.sum().plot(kind='bar')
plt.ylabel("Land Covered in Hectares (Ha)")
```
![image](images/lca_southasia_bar.png)




### **Study the Percent Change in the Forest Land in Subcontinent**

```python
# === Covered Forest Land ===

# Filter only tree-covered areas & Group
landcover = df_selected[df_selected["Item"] == "Tree-covered areas"]
landcover = landcover.groupby(["Area Code (ISO3)","Area","Year"])["Value"].sum().reset_index()

# Select only the base year (2001) and reference year (2018)
# and compute the percent change between them
landcover = landcover[(landcover["Year"] == 2001) | (landcover["Year"] == 2018)].reset_index()
landcover = landcover.pivot(index=["Area Code (ISO3)","Area"], columns = "Year", values="Value").reset_index()
landcover["%change"] = ((landcover[2018] - landcover[2001])/ landcover[2001])*100

landcover.head()
```
![image](images/tca_percent.png)

#### Visualizing the Percent Change in a Map for clear Understanding

```python

# Create the layout of the chart
title = '<b>Tree-Covered %Change in South Asia</b><br><sup>2018 vs 2001</sup>'
layout1 = go.Layout(title = {'text' : title, 
                            'x':0.5, 'xanchor': 'center'}, 
                   font = {"color" : 'black'},
                   width=980, height=600, plot_bgcolor="white", paper_bgcolor="white",
                   geo=dict(showframe=False, showcoastlines=False, projection_type='equirectangular'
                           )
                  )

# Create the figure
fig1 = go.Figure(layout = layout1)

# Create the Choropleth map tracing
trace1 = go.Choropleth(
    locations = landcover['Area Code (ISO3)'],
    z = landcover['%change'],
    text = landcover['Area'],
    colorscale = 'RdYlGn',
    autocolorscale=False,
    reversescale=False,
    marker_line_color='#2E2E2E',
    marker_line_width=0.5,
    colorbar_tickprefix = '%',
    colorbar_title = '%change',
)

fig1.add_trace(trace1)

fig1.show()
```
![image](images/tca_map.png)


### **Study the Percent Change in the Grass Land in Subcontinent**

```python
# === Covered Grass Land ===

# Filter only tree-covered areas & Group
landcover = df_selected[df_selected["Item"] == "Grassland"]
landcover = landcover.groupby(["Area Code (ISO3)","Area","Year"])["Value"].sum().reset_index()

# Select only the base year (2001) and reference year (2018)
# and compute the percent change between them
landcover = landcover[(landcover["Year"] == 2001) | (landcover["Year"] == 2018)].reset_index()
landcover = landcover.pivot(index=["Area Code (ISO3)","Area"], columns = "Year", values="Value").reset_index()
landcover["%change"] = ((landcover[2018] - landcover[2001])/ landcover[2001])*100

landcover.head()
```
![image](images/gca_percent.png)


#### Visualizing the Percent Change in a Map for clear Understanding

```python

# Create the layout of the chart
title = '<b>Grass Land %Change in Sub Continent</b><br><sup>2018 vs 2001</sup>'
layout1 = go.Layout(title = {'text' : title, 
                            'x':0.5, 'xanchor': 'center'}, 
                   font = {"color" : 'black'},
                   width=980, height=600, plot_bgcolor="white", paper_bgcolor="white",
                   geo=dict(showframe=False, showcoastlines=False, projection_type='equirectangular'
                           )
                  )

# Create the figure
fig1 = go.Figure(layout = layout1)

# Create the Choropleth map tracing
trace1 = go.Choropleth(
    locations = landcover['Area Code (ISO3)'],
    z = landcover['%change'],
    text = landcover['Area'],
    colorscale = 'RdYlGn',
    autocolorscale=False,
    reversescale=False,
    marker_line_color='#2E2E2E',
    marker_line_width=0.5,
    colorbar_tickprefix = '%',
    colorbar_title = '%change',
)

fig1.add_trace(trace1)

fig1.show()
```
![image](images/grass_land_map.png)

## Conclusion
This has been an extensive examination of the global Land Coverage over 18 years period. Globally, Russian Federation, China are among the countries with most land covered. In Land Coverage, Russian Federation, Brazil, Canada have the most forest land which are vital to life on Earth. Land Snow and Glaciers, which helps to reduce water scarcity are particularly in majority in Antarctica, Greenland, Canada.<br>
In subcontinent (India, Pakistan, Bangladesh) there is a change in Forest land and grassland. As compared to 2001, India have 3.55%, Pakistan have 39.06% increase in forest land on the other hand Bangladesh have 8.38% decrease in forest land in 2018. When it comes to grass covered land as compared to 2001, India have 18.42%, Pakistan have 10.42% descrease in 2018 on the other hand Bangladesh have 39.45% increase in 2018.<br>
Bangladesh should have to take measure to increase the number of forests because they are vital to life on earth and have positive impace on the environment.