
## A 前提
 - centOS 7.3 或 以上安装 java， openjdk 8 或 Oracle jdk 8  参看 [参考文献][1]      
 - &nbsp;安装 docker， yum install -y docker 
 - &nbsp; k8s.gcr.io 和 gcr.io    对应的国内镜像是 gcr.mirrors.ustc.edu.cn/google-containers

## B 安装k8s

1.  解压离线安装软件包 root登陆服务器后， 在k8s目录下有安装包，文件名为 `kube1.10.3min.tar.gz`
    
2.  添加参数，在安装包的conf目录下`kubeadm.yaml` 文件, 加添
``` 
    apiServerExtraArgs: 
        service-node-port-range: 80-32767
```    
3.  在master机上，进入shell子目录，
    
4.  `sh init.sh` 和 `sh master.sh`，并记录join的命令行
    
5.  在 node 机上，`sh init.sh`，然后执行master机记录下来的join命令。
    

## C 如果需要添加组件

1.  编辑 组件的pod yaml文件
    
2.  编辑 组件的svc yaml文件
    
3.  如有必要，编辑组件的deployment yaml文件
    
4.  编辑完成后，kubectl apply -f <pod yaml 文件名>
    
5.  kubectl apply -f <svc yaml 文件名>
    

## D 
如果架包更新了，我们取目前部署的Pod core-ykt 为例： 
```
kubectl delete service core-ykt 
kubectl delete pod core-ykt 
kubectl apply -f datalook_core_pod.yaml 
kubectl apply -f datalook_core_svc.yaml
```
如果需要检验的化，可以在日志中写下一些关键字，然后通过命令来看这个pod的日志 `kubectl logs <podname>  <podnamespace> `//显示pod日志, 比如 `kubectl logs core-ykt`看关键字是否出现。注意日志的级别，trace级别的日志 不会被打印。

## E 复原和清空kubernetes的环境

1.  清理所有kubernetes，请用`kubeadm reset`命令，并最好清理浏览器的缓存。
    
2.  在master上，进入shell目录， `sh init.sh` 和 `sh master.sh`
    
3.  进入ingress目录，Ingress目录在/root/k8s/ingress, 按下列顺序进行k8s集成. 
 - kubectl apply -f <pod yaml文件>
 - kubectl apply -f <svc yaml文件>
 - kubectl apply -f ingress_nginx_role_rb.yaml
 - kubectl apply -f nginx_default_backend.yaml
 - kubectl apply -f ingress_nginx_ctl.yaml
 - kubectl apply -f ingress_nginx_res.yaml
    

## F

1.  部署组件
    
2.  部署ingress或NodePort 如果需要暴露外部的接口
    

## G 常用命令

1.  `kubectl get po --all-namespaces` //显示pod列表
    
2.  `kubectl get svc --all-namespaces`//显示service列表
    
3.  `kubectl apply -f <*.yaml>` //用yaml文件设置，部署组件
    
4.  `kubectl logs <podname>  <podnamespace>` //显示pod日志
    
5.  `kubectl exec -it <podname> -- /bin/bash` //进入pod 的shell
    
6.  `kubeadm token create --print-join-command` //产生节点加入集群的命令行
    
7.  `kubectl run <podname> --image=<local image> --port=<expose port>` //执行本地镜像
    
8.  `docker save -o <imagename> <tarname.tar>` //从本地镜像中下载镜像，并打包成tar文件
    
9.  `docker pull <镜像地址>` //下载镜像到本地镜像中

10. `docker load -i <tarname.tar>` //将镜像上传到本地镜像库上

11. `kubectl exec -it <podname> -n kube-system -- bash`
    
12.  `kubectl delete -f fluentd-daemonset-elasticsearch.yaml`
    

## H 参考文献和视频

[how to install java on centos and fedora][1]
    
[kubernetes 离线包安装指南][2]

3. https://github.com/trisberg/boot-k8s-microservices/tree/s1p2017

4. https://medium.com/@maumribeiro/running-your-own-docker-images-in-minikube-for-windows-ea7383d931f6

5. https://blog.csdn.net/w1491413492/article/details/78827819

6. https://blog.csdn.net/levy_cui/article/details/70336283

7. https://www.youtube.com/watch?v=RelPurLZnII

8. http://www.iqiyi.com/w_19ruztmqil.html

9. http://lameleg.com/tech/install-k8s.html 


## I 排障经验

1.  gcr.mirrors.ustc.edu.cn 是gcr.io的国内镜像
    
2.  服务不能通过端口的不同来加以区别，比如端口5555是api服务的端口，端口8761是eureka的服务端口，那么不能用master:5555 和master：8761来路由到api和eureka的服务，而是通过不同的Url的路径master/api,和master/eureka来路由到不同的服务。
    
3.  看日志是最好的方法，看到问题在哪里，看日志的命令请参看e4.
    
4.  部署最好都用`kubectl apply -f <文件名.yaml>`, 而不是输入命令行。这样可以给以后重复使用。
    
5.  pod可以通过NodePort部署，但NodePort只是让容器暴露一个宿机的映射端口，作为高可靠性能来说，不推荐。NodePort的部署，一般是作为演示用。
    
6.  选择nginx作为ingress的反向代理，一定要有nginx的加载异常的服务，即一定要部署`nginx_default_backend.yaml`
    
7.  集成页面跳转时，端口会被忽略。 解决方案是在安装包的`conf`目录下`kubeadm.yaml` 文件, 加添 
```
    apiServerExtraArgs: 
        service-node-port-range: 80-32767
```    
8.  `ingress_nginx_res.yaml`文件中，加
```
       metadata: 
          name: my-ingress 
          namespace: default 
          annotations: nginx.ingress.kubernetes.io/ssl-redirect: "false"
```

[1]: https://www.digitalocean.com/community/tutorials/how-to-install-java-on-centos-and-fedora
[2]: https://segmentfault.com/a/1190000011707194
