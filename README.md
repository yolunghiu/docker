# Docker

## 容器与镜像

1. 镜像：是一个文件，只读的
2. 容器：镜像的运行环境
3. 下载镜像：`docker pull tomcat`
4. 查看镜像：`docker images`
5. 映射端口、运行容器：`docker run -p 8000:8080 tomcat`
6. 查看运行中的容器：`docker ps`
7. 查看所有容器：`docker ps -a`
8. 停止容器：`docker stop 9cea7f27a253`
9. 重新启动容器：`docker restart `
10. 删除容器：`docker rm 9cea7f27a253`
11. 删除镜像：先删除所有容器，然后`docker rmi ImageID`

## Dockerfile demo

1. 是Image镜像的描述文件
2. 创建一个交互终端：`docker exec -it 9cea7f27a253 /bin/bash`
3. 构建镜像：`docker build -t ark/docker-web:1.0 /home/ark/res/docker/02_dockerfile`
4. 查看镜像构建过程：`docker history image_id`

## Dockerfile 常用命令

1. FROM：基于基准镜像
   1. `FROM scratch `
   2. `FROM tomcat:9.0.22-jdk8-openjdk`
2. `LABEL version="1.0"`
3. WORKDIR：设置工作目录，类似cd命令
4. ADD
   1. `ADD hello /` ：将文件复制到根路径，可以自动解压缩、添加远程文件
5. ENV：设置环境常量
   1. `ENV JAVA_HOME /usr/local/openjdk8`
   2. `RUN ${JAVA_HOME}/bin/java-jar test.jar`
6. EXPOSE：将容器内部端口暴露给物理机
   1. `EXPOSE 8080`

## Docker分层

## Dockerfile运行命令

* RUN：构建时运行
  * `RUN apt install -y vim`
* ENTRYPOINT：容器启动时执行的命令
* CMD：设置默认执行的命令

## 发布镜像

## Link单向通信

* `docker run --name mydb centos sleep 1d` 启动第一个容器
* `docker run --name myweb centos sleep 1d`启动第二个容器
* `docker inspect container_id` 查看ip地址，进去两个容器可以ping通

> *上述方式不推荐使用，ip变了更改比较麻烦，可以实用link的方式，如下*

* `docker run --name myweb --link mydb -d centos sleep 1d` ,-d是静默模式，进去myweb容器后，直接`ping mydb` 即可ping通

## Bridge网桥双向通信

* `docker network create -d bridge my-bridge`
* demo
  * `docker run --name c1 -d centos sleep 1d`
  * `docker run --name c2 -d centos sleep 1d`
  * `docker network connect my-bridge c1`
  * `docker network connect my-bridge c2`

## Volume数据共享
- `docker run --name tomcat1 -v /home/ark/res/docker/04_volume/my-webapp:/usr/local/tomcat/webapps tomcat`
- 可以直接创建一个docker卷容器
  1. `docker create --name webpage -v /home/ark/res/docker/04_volume/my-webapp:/usr/local/tomcat/webapps tomcat /bin/true`
  2. `docker run -p 8001:8080 --volumes-from webpage --name tomcat1 -d tomcat`
  3. `docker run -p 8002:8080 --volumes-from webpage --name tomcat2 -d tomcat`
  4. `docker run -p 8003:8080 --volumes-from webpage --name tomcat3 -d tomcat`
- 使用`docker inspect container_id`，在输出中查找`Mounts`，可以找到数据挂载的`Source`和`Destination`路径
- 在Dockerfile中设置挂载点
  - 参考`04_volume/volume/Dockerfile`
  - `docker build -t ark/volume /home/ark/res/docker/04_volume/volume`
  - 在Dockerfile中设置挂载点不能直接设置本地磁盘目录，存放数据的目录是docker自动生成的，若要手动指定，需要使用`-v`命令设置

## Docker Compose
1. 它是单机多容器部署工具
2. 通过yml文件定义多容器如何部署
3. windows默认提供，linux需安装

### WordPress demo
1. ref:https://github.com/docker/awesome-compose/tree/master/official-documentation-samples/wordpress/
2. `docker compose up -d`，构建
3. `docker compose down --volumes`，删除

## 容器编排与k8s

### Pod
1. Pod是“容器”的容器，可以包含多个“container”
2. Pod是k8s的最小部署单元，一个Pod就是一个进程
3. Pod内部容器网络互通，每个Pod都是独立IP
4. Pod都是部署完整的应用或模块