

```py
import json
import pandas as pd
import plotly.express as px
import numpy as np

# to avoid making the jupyter notebook heavy

import plotly.io as pio
pio.renderers.default='chrome'
# then if we want to see any figure and call fig.show(),it will display in another tab and jupyter notebook will be easy 

indian_states_geojson = json.load(open('states_india_geojson.geojson','r'))
indian_states_geojson['features'][0]

df = pd.read_csv('https://raw.githubusercontent.com/nikhilkumarsingh/choropleth-python-tutorial/master/india_census.csv')
df.head()
```

## Apply preprocessing
- apply transformations to seperate the density to integer
- split it via "/" and replace "," with empty space "" 

```py

df['Density[a]'][3].split('/')[0].replace(",","")

df['Density']=df['Density[a]'].apply(lambda x : int(x.split("/")[0].replace(",","")) )
df['Density']

```

## mapping of geojson to df
- we have to do mapping from geojson features and the dataframe that we have, for that, we need to add id to each feature of geojson file 

```py

indian_states_geojson['features'][0].keys()

indian_states_geojson['features'][0]['properties']

indian_states_geojson['features'][0]['properties']['state_code']

state_id_map={}
for feature in indian_states_geojson['features']:
    # creating id to state code of properties of each feature
    feature['id'] = feature['properties']['state_code']
    # now we have to map id with state names, we are creating a dictionary->state_id_map, this will be handy to map ids with state names
    state_id_map[feature['properties']['st_nm']] = feature['id']

#map the id with state_id_map values
df['id']= df['State or union territory'].apply(lambda x : state_id_map[x] )
df['id']


# locations will be name of the column, whose values will be used to mapping with feature id,that is id column
fig = px.choropleth(df,locations=df['id'],geojson = indian_states_geojson,color='Density')
fig.show()
# here we have india is very small,

fig = px.choropleth(df,locations=df['id'],geojson = indian_states_geojson,color='Density',scope='asia')
fig.show()
# it will still show the countries in asia


fig = px.choropleth(df,locations=df['id'],geojson = indian_states_geojson,color='Density',scope='asia')
fig.update_geos(fitbounds='locations',visible=False)
fig.show()
# show only india
# here the spread of data is too much, w

df['Density']
# we have some values which are varying a lot 
# then we can convert these values to their logarithmic values ,since current visualisation is not showing good results 

np.log10(df['Density'])
# now the values are from 1 to 4 , we can call it as a new colum

df['DensityScale'] = np.log10(df['Density'])

fig = px.choropleth(df,locations='id',geojson=indian_states_geojson,color='DensityScale',scope='asia')
fig.update_geos(fitbounds='locations',visible=False)
fig.show()
# now we can see more color variantions 

```
## hover_data
- mention the attributes which you want to show 
```py
fig = px.choropleth(df,locations='id',geojson=indian_states_geojson,color='DensityScale',hover_name='State or union territory',hover_data=['Density']
                    scope='asia')
fig.update_geos(fitbounds='locations',visible=False)
fig.show()
# now we can see more color variantions 

```

## mapbox
- they created some functions which are wrappers to mapbox, 
- choropleth uses base map as mapbox