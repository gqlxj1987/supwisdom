树维目前的项目是以spring boot为主的项目，所以这里主要看将spring boot程序打包成镜像的例子。

## A. dockerfile 的编写：
```
#下载Oracle Java 8
1. FROM java:8
#容器能够读写宿机的目录/tmp
2. VOLUME /tmp
#复制 gearbox-rack-eureka-server.jar 到app.jar
3. ADD gearbox-rack-eureka-server.jar /app.jar
#更新 app.jar, 比如时间戳
4. RUN bash -c 'touch /app.jar'
#侦听端口 8761
5. EXPOSE 8761
#在容器里运行 app.jar
6. CMD ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/app.jar"]
```
## B.
私有镜像的[下载][1]:

修改`/usr/lib/systemd/system/docker.service` 

启动参数增加  
```
  --add-registry=<域名>:<端口号> 
  --insecure-registry=<域名>:<端口号>`
```

然后， 
     #装载修改后的参数
     `systemctl daemon-reload`
	 #重新启动docker
     `systemctl restart docker` 

 用docker info命令来确认最后一行Registries:是否包含似有上面的仓库地址


​	 
## C. 
pom.xml设置docker的plugin。 详细请看附录:[0].


## D 常用命令：
#1.列出镜像
`docker images`
#2. 删除镜像
`docker rmi <镜像名：版本>或<镜像hash号>`
#3. 取镜像
`docker pull <镜像名：版本>`

## E  排障经验
1. 如果有私有镜像，安装docker-ce的话，不能用B所阐述的方法。 docker安装请用命令`yum -y install docker`
2. 下载镜像，取镜像或删除镜像，请一定要记住不要遗漏版本号，缺省值是最新的版本，即latest。

/0/ 附录   
```
     <properties>
            <docker.image.repository>192.168.1.229:5000</docker.image.repository>
            <docker.image.prefix>platform</docker.image.prefix>
          </properties>
          <build>
            <finalName>${project.artifactId}</finalName>

        
        <plugins>
            <!-- 将 src/main/docker 下的 Dockerfile，复制到 target/docker 目录下 -->
            <plugin>
              <artifactId>maven-resources-plugin</artifactId>
              <version>3.0.1</version>
              <executions>
                <execution>
                  <id>prepare-dockerfile</id>
                  <phase>validate</phase>
                  <goals>
                    <goal>copy-resources</goal>
                  </goals>
                  <configuration>
                    <outputDirectory>${project.build.directory}/docker</outputDirectory>
                    <resources>
                      <resource>
                        <directory>${project.basedir}/src/main/docker</directory>
                        <filtering>true</filtering>
                      </resource>
                    </resources>
                  </configuration>
                </execution>
              </executions>
            </plugin>
    
    
            <!-- 依据 target/docker/Dockerfile 创建docker镜像 -->
            <plugin>
              <groupId>com.spotify</groupId>
              <artifactId>docker-maven-plugin</artifactId>
              <version>${docker.plugin.version}</version>
              <executions>
                <execution>
                  <id>build-image</id>
                  <phase>package</phase>
                  <goals>
                    <goal>build</goal>
                  </goals>
                </execution>
              </executions>
              <configuration>
                <imageName>${docker.image.server}/${docker.image.prefix}/${project.artifactId}</imageName>
                <imageTags>
                  <imageTag>${project.version}</imageTag>
                  <imageTag>latest</imageTag>
                </imageTags>
                <forceTags>true</forceTags>
                <dockerDirectory>${project.build.directory}/docker</dockerDirectory>
                <resources>
                  <resource>
                    <targetPath>/</targetPath>
                    <directory>${project.build.directory}</directory>
                    <include>${project.build.finalName}.jar</include>
                    <filtering>true</filtering>
                  </resource>
                </resources>
              </configuration>
            </plugin>
    
    
        </plugins>
      </build>
```
[1]: https://blog.csdn.net/huqigang/article/details/78364394
