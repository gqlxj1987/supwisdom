## 安装EFK

A：
 - 先从本地机下载elastic search： 
```
docker pull gcr.mirrors.ustc.edu.cn/google-containers/elasticsearch:v6.2.5
```
 -  
```
docker save -o elasticsearch.tar gcr.mirrors.ustc.edu.cn/google-containers/elasticsearch:v6.2.5 
```
 - scp tar文件到安装k8s的虚拟机上，
 - `docker load -i <tar文件>` 

B： 
`es-statefulset.yaml: image` 改为 `gcr.mirrors.ustc.edu.cn/google-containers/elasticsearch:v6.2.5`

C:
`fluentd-es-ds.yaml`:
`fluentd-elasticsearch:v2.2.0` 改为 `gcr.mirrors.ustc.edu.cn/google-containers/fluentd-elasticsearch:v2.2.0 (one could try v2.2.0)`

D:
`kibana-deployment`
`docker.elastic.co/kibana/kibana-oss:6.2.0`

E:
`chmod 777 /var/log`
`chmod 777 /var/lib/docker/containers`

F:
将svn中k8s/ingress/efk_1.10.3.tar 解压在一个目录下，然后再这个目录下，按顺序执行以下命令。

```
$ kubectl create -f es-statefulset.yaml
$ kubectl create -f es-service.yaml
$ kubectl create -f fluentd-es-configmap.yaml
$ kubectl create -f fluentd-es-ds.yaml
$ kubectl create -f kibana-deployment.yaml
$ kubectl create -f kibana-service.yaml
```
G:
```
kubectl exec -it <elastic-logging pod> -n kube-system -- /bin/bash
curl -XPUT 'http://elasticsearch-logging:9200/_cluster/settings' -H 'Content-Type: application/json' -d '{ "transient": { "cluster.routing.allocation.enable": "all" } }'
```

H:
```
$ kubectl proxy
$ firefox http://localhost:8001/api/v1/namespaces/kube-system/services/kibana-logging/proxy
```





