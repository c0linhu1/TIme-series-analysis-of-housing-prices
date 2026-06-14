# Time-series analysis of housing prices

This project uses InfluxDB 3 Core (a time-series database) to store and look at some
US housing/macro data from FRED. We pull three monthly series:

- case_shiller (CSUSHPINSA) - Case-Shiller national home price index
- mortgage_30yr (MORTGAGE30US) - 30 year fixed mortgage rate
- unemployment (UNRATE) - unemployment rate

## Files

- src/ingest.py - pulls the series from FRED and writes them into InfluxDB
- src/queries.py - runs 6 example SQL queries to show off the query language
- src/analysis.py - pulls the data back out and makes the figures in outputs/figures/

## What you need first

- Python 3.10+
- a free FRED api key (https://fredaccount.stlouisfed.org/apikeys)
- InfluxDB 3 Core installed (https://www.influxdata.com/downloads/)

## Setup

Install the python stuff:

```
pip install -r requirements.txt
```

Start the influx server in its own terminal and leave it running:

```
influxdb3 serve --node-id=local01 --object-store=file --data-dir ./influxdb3-data
```

Make the database:

```
influxdb3 create database housing --host http://localhost:8181 --token YOUR_TOKEN
```

If you re-run ingest a bunch you'll get duplicate points, so drop and recreate to start clean:

```
influxdb3 delete database housing --host http://localhost:8181 --token YOUR_TOKEN
influxdb3 create database housing --host http://localhost:8181 --token YOUR_TOKEN
```

You also need a .env file in the project root (it's gitignored) with these keys:

```
FRED_API_KEY=your_fred_api_key
INFLUX_HOST=http://localhost:8181
INFLUX_TOKEN=your_influx_token
INFLUX_DATABASE=housing
```

## Running it

From the project root, in this order:

```
python src/ingest.py     # load FRED data into influx
python src/queries.py     # print the 6 example queries
python src/analysis.py    # make the charts
```

## The queries

Each query in queries.py shows a different thing influx's SQL can do:

1. basic select with where/order by/limit
2. count + group by to see how many points per series
3. date_bin for a yearly average mortgage rate (time bucketing)
4. self join on time to put two series side by side
5. window function for a 12 month moving average
6. schema inspection through information_schema

## Outputs

- outputs/figures/01_series_overview.png - all three series indexed to 100 at jan 2000
- outputs/figures/02_example_analysis.png - case-shiller with a 12 month moving average

Data is all from FRED, starting 2000-01-01.
