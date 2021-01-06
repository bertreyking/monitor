 # [Prometheus 查询函数实操 ](https://prometheus.io/docs/prometheus/latest/querying/functions/)
 - label_join: 新增 label，并将源标签的 values 和 新增label 以 key=values的形式输出
 ```
 - 格式:
 label_join(v instant-vector, dst_label string, separator string, src_label_1, src_label_2)
 - 示例:
label_join(up{job="kubelet"},"new_key",",","instance","node_ip")  #若分割符不写会将第二个源标签 valeus就行赋值
 - 输出:
up{beta_kubernetes_io_arch="amd64",beta_kubernetes_io_os="linux",instance="k8s-master01",job="kubelet",kubernetes_io_arch="amd64",kubernetes_io_hostname="k8s-master01",kubernetes_io_os="linux",new_key="k8s-master01,10.6.203.60",node_ip="10.6.203.60",noderole="master"}
 ```
 
 - label_replace: 新增 label，并将源标签的 values 和 新增label 以 key=values的形式输出，且支持regex
 ```
 - 格式:
 label_replace(v instant-vector, dst_label string, replacement string, src_label string, regex string) #如果regex匹配不到，则按原数据进行显示
 - 示例:
 label_replace(up,"new_key","$1","instance","(.*):.*")
 label_replace(up,"new_key","$1","instance","(.*)")
 - 输出:
 up{instance="10.6.179.65:9090",job="prometheus",new_key="10.6.179.65"}
 up{instance="10.6.179.65:9090",job="prometheus",new_key="10.6.179.65:9090"}
 ```
