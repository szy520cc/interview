# Docker基础命令

以下是 Docker 日常操作中最常用的一些命令：

### 镜像管理 (`image`)

- **`docker pull <image_name>:<tag>`**: 从 Docker Hub 或其他镜像仓库拉取镜像。
  - 示例: `docker pull nginx:latest`

- **`docker images`**: 列出本地已有的所有镜像。

- **`docker build -t <new_image_name>:<tag> .`**: 根据当前目录下的 Dockerfile 构建一个新镜像。
  - `-t`: 指定新镜像的名称和标签。
  - `.`: Dockerfile 所在的路径。

- **`docker rmi <image_id_or_name>`**: 删除一个或多个指定的镜像。
  - `-f`: 强制删除。

- **`docker tag <source_image> <target_image>`**: 为镜像添加一个新的标签。

### 容器生命周期 (`container`)

- **`docker run [options] <image_name> [command]`**: 创建并启动一个新容器。
  - `-d`: 后台运行（detached mode）。
  - `-p <host_port>:<container_port>`: 端口映射。
  - `-v <host_path>:<container_path>`: 数据卷挂载。
  - `--name <container_name>`: 指定容器名称。
  - `it`: 交互式运行，通常用于进入容器 shell。
  - 示例: `docker run -d -p 8080:80 --name my-nginx nginx`

- **`docker ps`**: 列出当前正在运行的容器。
  - `-a`: 列出所有容器（包括已停止的）。

- **`docker stop <container_id_or_name>`**: 停止一个正在运行的容器。

- **`docker start <container_id_or_name>`**: 启动一个已停止的容器。

- **`docker restart <container_id_or_name>`**: 重启一个容器。

- **`docker rm <container_id_or_name>`**: 删除一个或多个已停止的容器。
  - `-f`: 强制删除正在运行的容器。

### 容器操作

- **`docker exec -it <container_id_or_name> <command>`**: 在正在运行的容器中执行一个命令。
  - 示例: `docker exec -it my-nginx /bin/bash` (进入容器的 shell)

- **`docker logs <container_id_or_name>`**: 查看容器的日志输出。
  - `-f`: 实时跟踪日志。

- **`docker inspect <container_id_or_name>`**: 获取容器或镜像的详细元数据信息。

### 系统管理

- **`docker system prune`**: 清理无用的 Docker 资源，如停止的容器、悬空的镜像、无用的网络和构建缓存。
  - `-a`: 清理所有未被使用的镜像（而不仅仅是悬空的）。
  - `--volumes`: 同时清理无用的数据卷。
