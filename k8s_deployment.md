# 前置要求

1. 每个服务器安装CentOS 7
2. 每个服务器安装OpenJDK 8或者Oracle JDK 8，可以[参考此处][1]
4. 每个服务器安装Docker，`yum install -y docker`

# 安装K8S集群

1. 上传离线包到每个服务器，并解压
2. 选定一台作为master，进入`${path-to-离线包}/shell`目录
3. 执行`sh init.sh`和`sh master.sh`，在最后会出现join node的命令行，记录下来
4. 到每个node机上，执行`sh init.sh`，然后执行master机记录下来的join命令

# 常用命令
1. 显示pod列表: `kubectl get po --all-namespaces`
2. 显示service列表: `kubectl get svc --all-namespaces`
3. 用yaml文件设置，部署组件: `kubectl apply -f *.yaml`
4. 显示pod日志: `kubectl logs podname podnamespace`
5. 进入pod 的shell: `kubectl exec -it podname -- /bin/bash`
6. 产生节点加入集群的命令行: `kubeadm token create --print-join-command` 

# 参考资料

* [kubernetes1.8与1.9.2安装指南，离线安装，内网安装, 使用外部etcd集群，coredns替换方法](https://segmentfault.com/a/1190000011707194)
* [boot-k8s-microservices](https://github.com/trisberg/boot-k8s-microservices/tree/s1p2017)
* [Running your own Docker containers in Minikube for Windows ](https://medium.com/@maumribeiro/running-your-own-docker-images-in-minikube-for-windows-ea7383d931f6)
* [windows系统minikube安装以及注意事项 ](https://blog.csdn.net/w1491413492/article/details/78827819)
* [Kubernetes系列05：深入掌握Service](https://blog.csdn.net/levy_cui/article/details/70336283)
* [Deploying Spring Boot Apps on Kubernetes - Thomas Risberg](https://www.youtube.com/watch?v=RelPurLZnII)
* [kubernetes安装教程](http://www.iqiyi.com/w_19ruztmqil.html)
* [Install K8S](http://lameleg.com/tech/install-k8s.html)

[1]: https://www.digitalocean.com/community/tutorials/how-to-install-java-on-centos-and-fedora
