# Grafana的使用技巧

1. dashboard中定义 Query variable 从而获取对应的label及label_values
```
- 格式如下：
Name                         Description
label_names()                Returns a list of label names.
label_values(label)          Returns a list of label values for the label in every metric.
label_values(metric, label)  Returns a list of label values for the label in the specified metric.
metrics(metric)              Returns a list of metrics matching the specified metric regex.
query_result(query)          Returns a list of Prometheus query result for the query.

- 示例如下：（grafana_Variables 输出）
label_values(node_filesystem_size_bytes,instance)

Preview of values
All 10.6.203.60:9100 10.6.203.62:9100 10.6.203.63:9100 10.6.203.64:9100

- 未经处理的数据如下：（prometheus_graph 输出）

node_filesystem_size_bytes{device="/dev/mapper/centos-root",fstype="xfs",instance="10.6.203.60:9100",job="node_exporter-metrics",mountpoint="/"}	50432839680
node_filesystem_size_bytes{device="/dev/mapper/centos-root",fstype="xfs",instance="10.6.203.62:9100",job="node_exporter-metrics",mountpoint="/"}

```

# 参考链接如下:
[grafana_prometheus](https://grafana.com/docs/grafana/latest/features/datasources/prometheus/) [templates-and-variables](https://grafana.com/docs/grafana/latest/variables/templates-and-variables/)
