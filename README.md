# Telegraf-Monitoring

I am sharing my code for Pi-Hole monitoring with you.
Feel free to use it in your projects.

## Telegraf Plugins
See the code in this .conf file: https://github.com/albortino/Telegraf-Monitoring/blob/72983d6e3105dda6097453fa64d89909a37558d0/telegraf.conf

## Grafana Queries (influxdb2)
### Monitor the activity similar to PiHole dashboard
Can be visualized with a time series chart.

```
from(bucket: "YOUR_BUCKET")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["_measurement"] == "MEASUREMENT_NAME")
  |> filter(fn: (r) => r["_field"] == "blocked" or r["_field"] == "cached" or r["_field"] == "forwarded")
  |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)
```


### Monitor the distribution of query types
Can be visualized with a 100% stacked bar chart.

```
from(bucket: "YOUR_BUCKET")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r._measurement == "MEASUREMENT_NAME")
  |> filter(fn: (r) => r._field == "reply_time")
  |> group(columns: ["type"])
  |> aggregateWindow(every: v.windowPeriod, fn: count, createEmpty: false)
  |> keep(columns: ["_time", "type", "_value"])
```


### Monitor mean reply time per query and type
```
from(bucket: "YOUR_BUCKET")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r._measurement == "MEASUREMENT_NAME")
  |> filter(fn: (r) => r._field == "reply_time")
  |> group(columns: ["type"])
  |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)
  |> keep(columns: ["_time", "type", "_value"])
```

Example:
![image](https://github.com/user-attachments/assets/19988ca6-fad6-41be-acea-3ead7c5e878b)



