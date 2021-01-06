# 记一次prometheus监控项kubelet丢失事件
无意中发现监控项kubelet丢失
# 分析步骤
- 查看配置文件，kubelet监控项存在
- 查看log，提示 open /etc/ssl/private/kubelet/tls.crt: no such file or directory"
- 查看sts 的yaml文件，发现之前添加 volumes和volumeMounts丢失了(kubelet-tls)

# 更改为永久生效
1. 将kubelet-tls密钥添加至 prometheus CRD下 k8s 资源中
```
kubectl get prometheus k8s -n monitoring -o yaml 
  secrets: #增加 kubelet-tls
  - kubelet-tls
```
2. 步骤1 执行后，prometheus的sts会自动重建pod，prometheus 下 会新增一个  volumeMounts / volumes
```
   volumeMounts:
   - mountPath: /etc/prometheus/secrets/kubelet-tls
     name: secret-kubelet-tls
     readOnly: true

  volumes:
  - name: secret-kubelet-tls
    secret:
      defaultMode: 420
      secretName: kubelet-tls
```
3. 查看发现taget依然没有,查看prometheus的log发现证书路径与volumeMounts不一致
```
kubectl logs -f prometheus-k8s-0 -n monitoring -c prometheus
level=error ts=2021-01-06T10:09:02.392Z caller=manager.go:188 component="scrape manager" msg="error creating new scrape pool" err="error creating HTTP client: unable to use specified client cert (/etc/ssl/private/kubelet/tls.crt) & key (/etc/ssl/private/kubelet/tls.key): open /etc/ssl/private/kubelet/tls.crt: no such file or directory" scrape_pool=monitoring/kubelet/0
level=error ts=2021-01-06T10:09:02.392Z caller=manager.go:188 component="scrape manager" msg="error creating new scrape pool" err="error creating HTTP client: unable to use specified client cert (/etc/ssl/private/kubelet/tls.crt) & key (/etc/ssl/private/kubelet/tls.key): open /etc/ssl/private/kubelet/tls.crt: no such file or directory" scrape_pool=monitoring/kubelet/1
```
4. 编辑servicemonitor下kubelet监控项
```
kubectl edit servicemonitor kubelet -n monitoring
%s#/etc/ssl/private/kubelet#/etc/prometheus/secrets/kubelet-tls#g # :(冒号)，全局替换保存退出，再次查看kubelet监控项已恢复
```
# 结论
- 因为之前是patch方式添加的volumes和volumemounts字段，所以在编辑 crds prometheus下的k8s资源后，重建了prometheus的pod，所以之前添加的配置丢失了
- 查看operator相关知识，说是可以在crd prometheus下的k8s资源中自定义volumes和volumemounts，但是尝试了几次不行，于是参照了etcd-certs的方式，并修改 servermointor 下kubelet后使其更改永久生效
