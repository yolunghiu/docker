# Docker Notes

## 安装

1. 卸载旧版本：`sudo apt-get remove docker docker-engine docker.io containerd runc`
2. 安装完成后，若出现权限问题，使用如下命令：`sudo chmod 666 /var/run/docker.sock`

## Commands

1. 列出镜像列表: `docker images`
2. 在容器内运行一个应用程序: `docker run ubuntu:15.10 /bin/echo "Hello world"`
3. 运行交互式的容器: `docker run -i -t ubuntu:15.10 /bin/bash`
4. 查看正在运行的容器: `docker ps`，created（已创建）、restarting（重启中）、running 或 Up（运行中）、removing（迁移中）、paused（暂停）、exited（停止）、dead（死亡）
5. 停止容器: `docker stop xxx`

## 使用容器

1. 获取镜像: `docker pull ubuntu`
2. 启动容器: `docker run -it ubuntu /bin/bash`
3. 查看所有容器: `docker ps -a`
4. 启动已停止运行的容器: `docker start b750bbbcfd88 `
5. 后台运行: `docker run -itd --name ubuntu-test ubuntu /bin/bash`
    - 加了 -d 参数默认不会进入容器，想要进入容器需要使用指令 docker exec
6. 停止一个容器: `docker stop <容器 ID>`
7. 重启一个容器: `docker restart <容器 ID>`
8. 进入容器: `docker exec -it 243c32535da7 /bin/bash`
9. 导出容器: `docker export 1e560fca3906 > ubuntu.tar`
10. 导入容器快照: `cat docker/ubuntu.tar | docker import - test/ubuntu:v1`
11. 删除容器: `docker rm -f 1e560fca3906`
12. 清理掉所有处于终止状态的容器: `docker container prune`

## 使用镜像

1. 删除镜像: `docker rmi hello-world`
2. resource: https://www.runoob.com/docker/docker-image-usage.html

## demo

1. 使用paddle镜像
    - `nvidia-docker run --name paddle_docker -it -v $PWD:/paddle registry.baidubce.com/paddlepaddle/paddle:2.2.1-gpu-cuda10.2-cudnn7 /bin/bash`
    - `--name paddle_docker`：设定Docker的名称，`paddle_docker`是自己设置的名称；
    - `-v $PWD:/paddle`：指定将当前路径（PWD变量会展开为当前路径的绝对路径）挂载到容器内部的 `/paddle` 目录；
2. 退出但不关闭容器：`Ctrl+p+q`
3. 退出并关闭容器：`exit`，再次进入需要先`docker restart`
4. 进入容器
    - `docker attach hello`：exit之后该容器就stop了
    - `docker exec -it hello /bin/bash`：exit之后该容器仍在运行

## 华为昇腾容器使用

1. 命令
    ```
    docker run -it --device=/dev/davinci0 --device=/dev/davinci_manager --device=/dev/devmm_svm --device=/dev/hisi_hdc -v /usr/local/dcmi:/usr/local/dcmi -p 9999:9999 -u root -v /usr/local/Ascend/driver:/usr/local/Ascend/driver -v /usr/local/bin/npu-smi:/usr/local/bin/npu-smi -v /usr/local/Ascend/driver/lib64/:/usr/local/Ascend/driver/lib64/ -v /usr/local/Ascend/driver/tools/:/usr/local/Ascend/driver/tools/ 10.10.10.156:1180/fz-1/edge-atlas-yolov5-sim:v8 /bin/bash
    ```
2. 参数解释
    - `-v 本地目录：容器中目录`建立映射
    - `-u root`root登录
