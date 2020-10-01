- Prometheus-tagets配置
```
### 检测prometheus_url状态
- job_name: 'blackbox-httpGET'
  metrics_path: /probe
  params:
    module: [http_2xx]
  static_configs:
    - targets:
      - http://10.6.179.107:9090 ###需要被检查的url链接
  relabel_configs: ###监控项labels定义
  - source_labels: [__address__]
    target_label: __param_target
  - source_labels: [__param_target]
    target_label: instance
  - target_label: __address__
    replacement: 10.6.179.65:9115 ###blackbox-exporter节点
    
### 检测节点ping状态检查
- job_name: 'blackbox-icmpPING'
  metrics_path: /probe
  params:
    module: [icmp]
  static_configs:
  - targets: ['10.6.179.60']
    labels:
      role: 'masternode'
  - targets: ['10.6.179.61','10.6.179.62','10.6.179.63','10.6.179.64','10.6.179.65'] ###需要被检查的节点
    labels:
      role: 'workernode'
  relabel_configs: ###监控项labels定义
    - source_labels: [__address__]
      regex: (.*)(:80)?
      target_label: __param_target
      replacement: ${1}
    - source_labels: [__param_target]
      target_label: instance
    - source_labels: [__param_target]
      regex: (.*)
      target_label: ping
      replacement: ${1}
    - source_labels: []
      regex: .*
      target_label: __address__
      replacement: 10.6.179.65:9115
```
- blackbox 配置
```
1. [download](https://github.com/prometheus/blackbox_exporter/releases)
2. 启动blackbox-exporter
./blackbox_exporter --config.file=blackbox.yml
- blackbox.yml ###默认配置文件即可
```
- dashboard制作
```
详情见json文件
```
