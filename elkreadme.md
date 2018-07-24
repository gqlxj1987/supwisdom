
## 日志ELK系统Readme
					
1. ELK 已经在一卡通ykt服务器部署完毕
2. 根据先前和赵工（赵奎强）的沟通定义为以下三种格式：

a) 标签为info是spring系统日志，例如：
```　
2018-04-27 10:42:23 [RMI TCP Connection(10)-127.0.0.1] - INFO  - org.apache.catalina.core.ContainerBase.[Tomcat].[localhost].[/] - Initializing Spring FrameworkServlet 'dispatcherServlet'
```
b) 标签为app-info的自己的业务日志，例如：
```
2018-04-27 10:42:49 [http-nio-8088-exec-1] - INFO  - app-info - injectip ip 192.168.16.89
```
c) 标签为app-info-exception-info的多行异常日志，例如
```
2018-04-27 10:53:17 [http-nio-8088-exec-4] - ERROR - app-info-exception-info - params:{"cardid":"111111111","txamt":10,"ip":"192.168.16.89","stationcode":"0002","inputuserid":1,"organcode":"99999"} java.lang.NullPointerException: null
	at com.datalook.group.BusinessHandler.handler(BusinessHandler.java:93) ~[classes/:?]
	at com.datalook.group.BusinessGroupController.businessGroup(BusinessGroupController.java:51) [classes/:?]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:1.8.0_77]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[?:1.8.0_77]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_77]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_77]
	。。。
```
3. 现在的演示日志是放在`192.168.16.37`的`/root/elk/test.log`文件里，实际的时候可以把日志放在一个目录下，然后修改`/etc/filebeat/filebeat.yml`中的`filebeat.prospectors:`的paths关键字。

4. 如果要修改日志的格式，那么同时，也要修改`/etc/logstash/first-pipelines.conf`的正则表达式。

5. 视觉效果图，先要写查询语句，然后根据需求添加视觉效果图，视觉效果图可以存在dashboard里。

6. 启动ELK： 请根据顺序 <br/>
a）elasticsearch， 请先改变参数文件（`/etc/elasticsearch/elasticsearch.yml`的绑定（binding）参数。 
  安装用下面的命令：
  `sudo rpm --install elasticsearch-6.3.0.rpm`
  
  &nbsp;&nbsp;&nbsp;  安装完毕后，可以激活service，有以下命令：
  ```
  sudo systemctl daemon-reload
  sudo systemctl enable elasticsearch.service

  sudo systemctl start elasticsearch.service
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; b) Kibana， 请先改变参数文件（`/etc/kibana/kibana.yml`)的绑定（binding）参数，
安装用以下命令：
  `sudo rpm --install kibana-6.3.0-x86_64.rpm`

安装完毕后，可以
```
  sudo systemctl daemon-reload
  sudo systemctl enable kibana.service

  sudo systemctl start kibana.service
  ```
  暂停Kibana服务，可以用
 ` sudo systemctl stop kibana.service`

&nbsp;&nbsp;&nbsp; c)logstash, 请修改`first-pipelines.conf`文件，加入正则表达式，安装完毕后，启动logstash
  安装用下面的命令：
  `sudo rpm --install xxx.rpm`
  启动命令：
  ```
  /usr/share/logstash/bin/logstash -f /etc/logstash/first-pipelines.conf --config.reload.automatic
  ```
   
&nbsp;&nbsp;&nbsp; d）filebeat，请修改`/etc/filebeat/filebeat.yml` 参数，主要是读取日志文件的文件夹，和异常报错的多行参数，然后启动filebeat
  安装用下面的命令：
  
  `sudo rpm --install xxx.rpm`
  
  启动命令：
  
  `/usr/share/filebeat/bin/filebeat -e -c /etc/filebeat/filebeat.yml -d "publish"`
 
