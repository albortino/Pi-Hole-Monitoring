# Telegraf-Monitoring

I am sharing my code for Pi-Hole monitoring with you.
Feel free to use it in your projects.

- telegraf plugins (https://github.com/albortino/Telegraf-Monitoring/blob/72983d6e3105dda6097453fa64d89909a37558d0/telegraf.conf)
- grafana queries (influxdb2)


## Monitor the activity similar to PiHole dashboard
Can be visualized with a time series chart.

```
from(bucket: "YOUR_BUCKET")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["_measurement"] == "MEASUREMENT_NAME")
  |> filter(fn: (r) => r["_field"] == "blocked" or r["_field"] == "cached" or r["_field"] == "forwarded")
  |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)
```


## Monitor the distribution of query types
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


## Monitor mean reply time per query and type
```
from(bucket: "YOUR_BUCKET")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r._measurement == "MEASUREMENT_NAME")
  |> filter(fn: (r) => r._field == "reply_time")
  |> derivative(unit: 1s)
  |> group(columns: ["type"])
  |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)
  |> keep(columns: ["_time", "type", "_value"])
```

Example:
![image](https://github.com/user-attachments/assets/9c9339d2-2da2-4fb4-a0f4-a61668ada257)


