```py
import plotly.express as px
import pandas as pd


df=pd.read_csv(r'owid-covid-data.csv')

fig = px.choropleth(df,locations='iso_code',color='new_cases',hover_name='location',animation_frame='date')
fig.show()

px.choropleth(df,locations='iso_code',color='new_cases',hover_name='location',animation_frame='date')

fig  = px.choropleth(df,locations='iso_code',color='new_cases',hover_name='location',projection='natural earth',color_continuous_scale='RdYlGn', animation_frame='date')

fig.show()
