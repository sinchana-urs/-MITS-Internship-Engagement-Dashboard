import pandas as pd
import plotly.express as px
from dash import Dash, dcc, html, Input, Output

# Load real data from CSV
df = pd.read_csv("internship_data.csv")  # Your actual CSV file

# Clean data
df['Application_Date'] = pd.to_datetime(df['Application_Date'], errors='coerce')
df.dropna(subset=['Application_Date'], inplace=True)
df['Internship_Domain'] = df['Internship_Domain'].str.title().str.strip()
df['Month'] = df['Application_Date'].dt.to_period('M').astype(str)

# Dash app setup continues below...
# --------- Step 2: Initialize Dash App ---------
app = Dash(__name__)
app.title = "MITS Internship Dashboard"

# --------- Step 3: Layout ---------
app.layout = html.Div([
    html.H1("📊 MITS Internship Engagement Dashboard", style={'textAlign': 'center'}),
    
    html.Div([
        html.Label("Filter by Department:"),
        dcc.Dropdown(
            id='department-filter',
            options=[{'label': dept, 'value': dept} for dept in sorted(df['Department'].unique())],
            value=None,
            placeholder="Select a department",
            clearable=True
        ),
    ], style={'width': '48%', 'display': 'inline-block', 'padding': '0px 20px'}),

    dcc.Graph(id='bar-chart'),
    dcc.Graph(id='line-chart')
])

# --------- Step 4: Callbacks ---------
@app.callback(
    [Output('bar-chart', 'figure'),
     Output('line-chart', 'figure')],
    [Input('department-filter', 'value')]
)
def update_charts(dept_filter):
    filtered_df = df if dept_filter is None else df[df['Department'] == dept_filter]

    # Popular Domains Bar Chart
    domain_counts = filtered_df['Internship_Domain'].value_counts().reset_index()
    domain_counts.columns = ['Internship_Domain', 'Count']
    bar_fig = px.bar(domain_counts.head(10), x='Count', y='Internship_Domain', orientation='h',
                     title='Top Internship Domains', color='Count', color_continuous_scale='teal')

    # Emerging Domains Line Chart
    time_series = filtered_df.groupby(['Month', 'Internship_Domain']).size().reset_index(name='Applications')
    top_domains = domain_counts['Internship_Domain'].head(5).tolist()
    time_series = time_series[time_series['Internship_Domain'].isin(top_domains)]
    line_fig = px.line(time_series, x='Month', y='Applications', color='Internship_Domain',
                       title='Monthly Trends for Top Domains')

    return bar_fig, line_fig

# --------- Step 5: Run App ---------
if __name__ == '__main__':
    app.run(debug=True)
