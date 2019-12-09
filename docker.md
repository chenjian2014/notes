# docker



## 创建镜像

#### 基于已有镜像的容器创建

docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

#### 基于 Dockerfile 创建

Dockerfile 分成四个部分: 基础镜像信息, 维护者信息, 镜像操作指令, 容器启动时执行命令

```shell
FROM python:3.7.5  # 基础镜像

MAINTAINER docker_user docker_user@email.com   # 维护者信息

ENV DEPLOY_ENV test # 设置环境变量
ENV DEBUG true # 设置环境变量

COPY requirements.txt /  # 复制文件到 docker 目录

RUN pip install --upgrade pip \
    && pip install --upgrade setuptools \
    && pip install -r /requirements.txt        # 镜像操作指定, 这里安装 python 依赖

COPY ./ /rubik   # 复制代码

WORKDIR /rubik   # 指定 docker 的当前目录, 这样后续执行命令不需要 cd 到指定目录

CMD gunicorn -c ./gunicorn.py rubik.app:app  # 容器执行命令, 一般用于启动服务

```

编写完 Dockerfile 后, 使用 docker build 创建镜像

```shell
docker build -t imageName:tag path     # path 为 Dockerfile 文件所在目录
```



## 启动容器

#### 新建并启动

```shell
# -p 为端口映射, -e 为环境变量, -v 为文件夹映射
docker run -p 80:8000 -e DEBUG=true -v /local:/data image:tag
```



## 搭建私有仓库 

harbor



## 与仓库交互

```shell
# 登陆
docker login -u username -p password server

# 拉取镜像
docker pull name:tag

# 打标签
# 一般推到远程的 image 格式为 [域名/path/image:tag]
# 示例: docker tag ubuntu:24.04 10.0.2.2:8000/myproject/ubuntu:24.04
docker tag source_image:tag target_image:tag 

# 推送镜像到仓库
docker push name:tag
```



## docker compose

用于定义和运行多个容器

允许用户只编写一个 docker-compose.yaml 文件来定义一组相关联的应用容器作为一个完整的项目

#### pip安装

```shell
pip install docker-compose
```

#### 编写 docker-compose.yaml 文件

主要定义 **services**, **networks**, **volumes**

日常使用的是定义 services

```yaml
version: "3.7"
services:

  webapp:
    build: ./dir  # build 还可以作为一个 object, 指定 Dockerfile 文件
    container_name: webapp
    environment:  # 设置环境变量
      - DEBUG=True
    ports:
      - "80:80"  # 指定端口
    volumes:     # volumes 有更复杂的格式, 可以挂载数据卷容器
    	- ./dir:/root/code
    restart: always  # 默认为 no
    depends_on:  # 安装依赖顺序启动容器
    	- redis
    
  redis:
    image: redis
volumes:
  example:  # 这是一个示例的数据卷容器, 注意 : 表示这是一个 object, 下面可以配置参数, 只是这里省略了.
```

#### 运行

```shell
# 最常用的方式, 将自动完成构建镜像, 创建服务, 启动服务, 关联服务相关容器等一系列操作.
docker-compose up  # -d 表示后台构建并启动
```

## kubernetes

文档: https://jimmysong.io/kubernetes-handbook/concepts/pod-overview.html

