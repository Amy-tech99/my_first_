# my_first_)

g = combine_gsc_to_gtrends(df, trend_data)

g = compute_seasonality(g, 'interest')

# dropping first and las row as they are often and incomplete week
# g = g.drop(0).reset_index(drop=True)
# g.drop(g.tail(1).index,inplace=True)
# make prediction
g['prediction'] = g['clicks']
for i in range(len(g)):
    if i == 0:
        g.loc[i,'prediction'] = g.loc[i,'clicks']
    else:
        prev_clicks = g.loc[i-1,'prediction'] 
        perc_variation = g.loc[i,'seasonnality_index']
        pred = prev_clicks * perc_variation
        # print(i, g.loc[i,'date'], g.loc[i,'clicks'], g.loc[i,'interest'])
        # print(prev_clicks, perc_variation, pred) 
        g.loc[i,'prediction'] = pred

# plot the graph
fig = make_subplots(specs=[[{"secondary_y": True}]])
fig.add_trace(
    go.Scatter(
        x=g['date'], 
        y=g['prediction'], 
        name='Prediction'
        ),
        secondary_y=False
    )
fig.add_trace(
    go.Scatter(
        x=g['date'],
        y=g['clicks'],
        name='Clicks'),
    secondary_y=False
    )
fig.add_trace(
    go.Scatter(
        x=g['date'],
        y=g['interest'],
        name='Trend'),
    secondary_y=True
    )
fig.update_layout(title_text=f'Clicks Vs Prediction - {property_url}')
fig.update_xaxes(title_text='Date')
fig.update_yaxes(title_text="<b>Traffic:</b> Pred VS Actual", secondary_y=False)
fig.update_yaxes(title_text="<b>Google Trends</b> data", secondary_y=True)

fig.show()
