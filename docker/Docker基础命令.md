好的，我将尽可能全面地列举 Docker 常用的基础命令、常用参数及示例，并提供详细的中文解释。

为了便于理解和查找，我将命令分为以下几大类：

1.  **容器生命周期管理**
2.  **镜像管理**
3.  **容器操作与信息查看**
4.  **系统级管理**
5.  **网络管理**
6.  **数据卷管理**

---

## Docker 常用基础命令、参数与示例

---

### **1. 容器生命周期管理**

这部分命令用于管理 Docker 容器的创建、启动、停止、重启、删除等生命周期操作。

| 命令                 | 常用参数                                | 示例                                                                                | 中文解释                                                                                                                                                                                                                                           |
| :------------------- | :-------------------------------------- | :---------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `docker run`         | `-d` (后台运行)                         | `docker run -d --name mynginx -p 80:80 nginx:latest`                                | 创建并启动一个新容器。如果本地没有指定镜像，会自动从 Docker Hub 拉取。                                                                                                                                                                |
|                      | `--name <name>` (指定容器名)            |                                                                                     | `-d`: 将容器置于后台运行。`--name`: 为容器指定一个易于识别的名称。                                                                                                                                                                  |
|                      | `-p <host_port>:<container_port>` (端口映射) |                                                                                     | `-p`: 将主机端口映射到容器端口。`80:80` 表示将主机上的 80 端口映射到容器内的 80 端口。                                                                                                                                                |
|                      | `-it` (交互式终端)                      | `docker run -it ubuntu:latest bash`                                                 | `-i`: 保持标准输入打开。`-t`: 分配一个伪终端。通常用于进入容器内部进行交互式操作。                                                                                                                                                 |
|                      | `--rm` (容器退出后自动删除)             | `docker run --rm -it alpine:latest sh`                                              | `--rm`: 容器退出后自动删除。适用于临时容器。                                                                                                                                                                                        |
|                      | `-v <host_path>:<container_path>` (数据卷挂载) | `docker run -v /data/nginx_logs:/var/log/nginx --name mynginx -d nginx`           | `-v`: 将主机上的目录或文件挂载到容器中，实现数据持久化或共享。                                                                                                                                                                        |
|                      | `--network <network_name>` (指定网络)   | `docker run --network my_custom_net --name myapp -d myapp_image`                    | `--network`: 将容器连接到指定的用户定义网络。                                                                                                                                                                                       |
|                      | `--env <KEY=VALUE>` 或 `-e <KEY=VALUE>` (设置环境变量) | `docker run -e MY_VAR=hello_world --name mycontainer -d ubuntu tail -f /dev/null` | `-e`: 在容器内设置环境变量。                                                                                                                                                                                           |
| `docker create`      | 与 `docker run` 几乎相同的所有参数      | `docker create --name mycreatedcontainer nginx:latest`                              | 只创建一个新容器，但不启动它。这允许你先配置容器，然后随时使用 `docker start` 启动。                                                                                                                                                   |
| `docker start`       | `<container_id_or_name>`                | `docker start mynginx`                                                              | 启动一个或多个已停止的容器。                                                                                                                                                                                                         |
| `docker stop`        | `<container_id_or_name>`                | `docker stop mynginx`                                                               | 停止一个或多个正在运行的容器。Docker 会先向容器发送 SIGTERM 信号，等待一段时间（默认 10 秒）后如果容器仍未停止，会发送 SIGKILL 信号强制终止。                                                                                                |
| `docker kill`        | `<container_id_or_name>`                | `docker kill mynginx`                                                               | 强制停止一个或多个正在运行的容器。直接发送 SIGKILL 信号，容器不会优雅关闭，数据可能丢失。通常作为 `docker stop` 失败后的备用方案。                                                                                                       |
| `docker restart`     | `<container_id_or_name>`                | `docker restart mynginx`                                                            | 重启一个或多个容器。等同于先 `stop` 后 `start`。                                                                                                                                                                                     |
| `docker pause`       | `<container_id_or_name>`                | `docker pause mynginx`                                                              | 暂停一个或多个正在运行的容器中的所有进程。容器的状态变为“Paused”。                                                                                                                                                                       |
| `docker unpause`     | `<container_id_or_name>`                | `docker unpause mynginx`                                                            | 取消暂停一个或多个被暂停的容器。                                                                                                                                                                                                     |
| `docker rm`          | `<container_id_or_name>`                | `docker rm mynginx`                                                                 | 删除一个或多个已停止的容器。                                                                                                                                                                                                         |
|                      | `-f` (强制删除)                         | `docker rm -f myrunningcontainer`                                                   | `-f`: 强制删除正在运行的容器。                                                                                                                                                                                                       |
|                      | `-v` (删除关联的数据卷)                 | `docker rm -v mycontainer_with_volume`                                              | `-v`: 同时删除容器关联的匿名数据卷。                                                                                                                                                                                                 |
| `docker prune`       | `container` (子命令)                    | `docker container prune`                                                            | 删除所有已停止的容器。通常用于清理废弃容器。                                                                                                                                                                                         |
|                      | `-f` (强制执行，不提示)                 | `docker container prune -f`                                                         | `-f`: 强制执行，不进行确认提示。                                                                                                                                                                                                     |

---

### **2. 镜像管理**

这部分命令用于管理 Docker 镜像的获取、构建、列出、删除等。

| 命令               | 常用参数                 | 示例                                                                  | 中文解释                                                                                                                                                                                                                                              |
| :----------------- | :----------------------- | :-------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `docker pull`      | `<image_name>:<tag>`     | `docker pull ubuntu:20.04`                                            | 从 Docker 镜像仓库（默认是 Docker Hub）拉取一个镜像。如果不指定标签，默认拉取 `latest` 标签。                                                                                                                                                   |
| `docker push`      | `<repo_name>/<image_name>:<tag>` | `docker push myregistry/myapp:v1.0`                                   | 将本地的一个镜像推送到远程镜像仓库。需要先使用 `docker tag` 给镜像打上包含仓库地址的标签。                                                                                                                                                |
| `docker images`    |                          | `docker images`                                                       | 列出本地主机上所有的 Docker 镜像。                                                                                                                                                                                                  |
|                    | `-a` (显示所有镜像)      | `docker images -a`                                                    | `-a`: 显示所有镜像，包括中间层镜像。                                                                                                                                                                                                |
|                    | `-q` (只显示镜像ID)      | `docker images -q`                                                    | `-q`: 只显示镜像的 ID。常用于配合其他命令批量操作。                                                                                                                                                                                 |
| `docker rmi`       | `<image_id_or_name>`     | `docker rmi ubuntu:latest`                                            | 删除一个或多个本地镜像。如果镜像正在被容器使用，需要先删除容器或使用 `-f` 强制删除。                                                                                                                                                       |
|                    | `-f` (强制删除)          | `docker rmi -f myimage`                                               | `-f`: 强制删除镜像，即使有容器在使用它（但会先删除关联容器）。                                                                                                                                                                          |
| `docker build`     | `-t <name>:<tag>` (指定镜像名和标签) | `docker build -t myapp:latest .`                                      | 从 Dockerfile 构建镜像。`.` 表示 Dockerfile 位于当前目录。                                                                                                                                                                             |
|                    | `-f <Dockerfile_path>` (指定 Dockerfile 路径) | `docker build -f /path/to/Dockerfile -t myapp:v1.0 .`                 | `-f`: 指定 Dockerfile 的路径，而不是默认在当前目录查找。                                                                                                                                                                              |
|                    | `--no-cache` (禁用缓存)  | `docker build --no-cache -t myapp:latest .`                           | `--no-cache`: 构建时不使用缓存，强制重新执行所有构建步骤。                                                                                                                                                                          |
| `docker tag`       | `<source_image>:<source_tag> <target_image>:<target_tag>` | `docker tag myapp:latest myregistry/myapp:v1.0`                         | 为一个镜像添加一个新的标签（别名）。通常用于将本地镜像打上远程仓库的标签，以便推送。                                                                                                                                                    |
| `docker history`   | `<image_id_or_name>`     | `docker history nginx:latest`                                         | 查看一个 Docker 镜像的构建历史和每一层的大小。                                                                                                                                                                                        |
| `docker save`      | `-o <output_file>`       | `docker save -o nginx.tar nginx:latest`                               | 将一个或多个镜像保存到 tar 文件中。可用于离线传输镜像。                                                                                                                                                                               |
| `docker load`      | `-i <input_file>`        | `docker load -i nginx.tar`                                            | 从 tar 文件中加载一个或多个镜像到本地。通常用于加载 `docker save` 命令保存的镜像。                                                                                                                                                    |
| `docker import`    | `<file> [<repository>[:<tag>]]` | `docker import mycontainer.tar myimage:v1.0`                          | 从一个归档文件（通常是 `docker export` 导出的容器文件系统快照）导入为一个新的镜像。与 `docker load` 的区别是，`import` 导入的是文件系统快照，不包含历史层信息，而 `load` 导入的是完整的镜像（包含所有层）。                        |
| `docker prune`     | `image` (子命令)         | `docker image prune`                                                  | 删除所有悬空镜像（dangling images），即没有被任何标签引用的镜像。                                                                                                                                                                     |
|                    | `-a` (删除所有未被使用的镜像) | `docker image prune -a`                                               | `-a`: 删除所有未被使用的镜像（包括悬空镜像和没有容器引用的镜像）。慎用此命令，它会删除所有未运行容器使用的镜像。                                                                                                                       |

---

### **3. 容器操作与信息查看**

这部分命令用于与运行中的容器进行交互，以及查看容器的各种信息。

| 命令                | 常用参数                              | 示例                                                               | 中文解释                                                                                                                                                                                                                                              |
| :------------------ | :------------------------------------ | :----------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `docker ps`         | `-a` (显示所有容器)                   | `docker ps -a`                                                     | 列出所有正在运行的容器。`docker ps -a` 显示所有容器（包括已停止的）。                                                                                                                                                                  |
|                     | `-q` (只显示容器ID)                   | `docker ps -q`                                                     | `-q`: 只显示容器的 ID。常用于配合其他命令批量操作。                                                                                                                                                                                     |
|                     | `-s` (显示总文件大小)                 | `docker ps -s`                                                     | `-s`: 显示容器使用的总文件大小。                                                                                                                                                                                                      |
|                     | `-f <filter>` (按条件过滤)            | `docker ps -a -f status=exited`                                    | `-f`: 按指定条件过滤容器。例如，`status=exited` 显示所有已退出的容器。                                                                                                                                                                 |
| `docker logs`       | `<container_id_or_name>`              | `docker logs mynginx`                                              | 查看容器的标准输出日志。                                                                                                                                                                                                            |
|                     | `-f` (实时跟踪日志)                   | `docker logs -f mynginx`                                           | `-f`: 实时跟踪容器的日志输出。                                                                                                                                                                                                        |
|                     | `--tail <num>` (显示末尾N行)          | `docker logs --tail 100 mynginx`                                   | `--tail`: 只显示日志的最后 N 行。                                                                                                                                                                                                   |
|                     | `--since <timestamp>` (显示指定时间后的日志) | `docker logs --since "2023-01-01T00:00:00"`                        | `--since`: 显示从指定时间戳或相对时间（如 `10m`）以来的日志。                                                                                                                                                                          |
| `docker exec`       | `-it` (交互式)                        | `docker exec -it mynginx bash`                                     | 在正在运行的容器中执行命令。`bash` 或 `sh` 用于进入容器的 shell。                                                                                                                                                                     |
|                     | `<command>` (要执行的命令)            | `docker exec mynginx ls -l /usr/share/nginx/html`                  | 在容器内执行非交互式命令。                                                                                                                                                                                                          |
| `docker inspect`    | `<container_id_or_name> / <image_id_or_name>` | `docker inspect mynginx` 或 `docker inspect nginx:latest`          | 显示一个或多个 Docker 对象（容器、镜像、网络、数据卷等）的详细低级信息，通常以 JSON 格式输出。                                                                                                                                            |
| `docker top`        | `<container_id_or_name>`              | `docker top mynginx`                                               | 查看容器中正在运行的进程。类似于 Linux 的 `top` 命令。                                                                                                                                                                              |
| `docker stats`      |                                       | `docker stats`                                                     | 实时显示一个或多个正在运行的容器的资源使用情况（CPU、内存、网络 I/O 等）。                                                                                                                                                            |
| `docker cp`         | `<source_path> <destination_path>`    | `docker cp mynginx:/etc/nginx/nginx.conf ./`                       | 在容器和主机之间复制文件或目录。支持从容器复制到主机，或从主机复制到容器。                                                                                                                                                            |
| `docker rename`     | `<old_name> <new_name>`               | `docker rename old_nginx_name new_nginx_name`                      | 重命名一个容器。                                                                                                                                                                                                      |
| `docker diff`       | `<container_id_or_name>`              | `docker diff mynginx`                                              | 查看容器文件系统自镜像创建以来所做的更改（A-添加，C-更改，D-删除）。                                                                                                                                                                |
| `docker port`       | `<container_id_or_name>`              | `docker port mynginx`                                              | 显示容器的端口映射。                                                                                                                                                                                                  |

---

### **4. 系统级管理**

这些命令用于管理 Docker 守护进程和清理 Docker 环境。

| 命令               | 常用参数              | 示例                                     | 中文解释                                                                                                                                                                 |
| :----------------- | :-------------------- | :--------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `docker version`   |                       | `docker version`                         | 显示 Docker 客户端和守护进程的版本信息。                                                                                                                                 |
| `docker info`      |                       | `docker info`                            | 显示 Docker 系统级别的详细信息，包括容器、镜像、存储驱动、运行时、日志驱动、内核版本等。                                                                                 |
| `docker system df` |                       | `docker system df`                       | 显示 Docker 磁盘使用情况，包括镜像、容器、本地数据卷等。                                                                                                                 |
| `docker system prune` | `-a` (删除所有未使用的) | `docker system prune -a`                 | 清理 Docker 系统中的所有未使用的资源，包括停止的容器、悬空镜像、未使用的网络和构建缓存。`docker system prune` 默认只删除停止的容器和悬空镜像。`-a` 删除所有未使用的镜像。 |
|                    | `--volumes` (同时删除未使用的卷) | `docker system prune --volumes` 或 `docker system prune -a --volumes` | `--volumes`: 在清理时同时删除所有未使用的本地数据卷。                                                                                                                    |

---

### **5. 网络管理**

这些命令用于创建、管理和检查 Docker 网络。

| 命令              | 常用参数                     | 示例                                                             | 中文解释                                                                                                                                                                 |
| :---------------- | :--------------------------- | :--------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `docker network ls` |                              | `docker network ls`                                              | 列出 Docker 中的所有网络。                                                                                                                                               |
| `docker network create` | `--driver <driver_name>` (指定驱动) | `docker network create --driver bridge my_custom_network`        | 创建一个新的用户定义网络。`bridge` 是最常用的驱动。                                                                                                                      |
|                   | `--subnet <subnet>` (指定子网) | `docker network create --subnet 172.18.0.0/16 my_custom_network` | `--subnet`: 为网络指定 IP 地址子网。                                                                                                                                    |
| `docker network rm` | `<network_id_or_name>`       | `docker network rm my_custom_network`                            | 删除一个或多个用户定义网络。                                                                                                                                             |
| `docker network connect` | `<network_id_or_name> <container_id_or_name>` | `docker network connect my_custom_network mynginx`               | 将一个正在运行的容器连接到指定网络。                                                                                                                                     |
| `docker network disconnect` | `<network_id_or_name> <container_id_or_name>` | `docker network disconnect my_custom_network mynginx`            | 将一个容器从指定网络中分离。                                                                                                                                             |
| `docker network inspect` | `<network_id_or_name>`       | `docker network inspect my_custom_network`                       | 显示一个或多个网络的详细信息，包括连接到该网络的容器。                                                                                                                   |

---

### **6. 数据卷管理**

这些命令用于创建、管理和检查 Docker 数据卷，用于数据持久化。

| 命令              | 常用参数              | 示例                                     | 中文解释                                                                                                               |
| :---------------- | :-------------------- | :--------------------------------------- | :----------------------------------------------------------------------------------------------------------------------- |
| `docker volume create` | `<volume_name>`       | `docker volume create my_data_volume`    | 创建一个命名数据卷。                                                                                                   |
| `docker volume ls` |                       | `docker volume ls`                       | 列出所有本地数据卷。                                                                                                   |
| `docker volume rm` | `<volume_name>`       | `docker volume rm my_data_volume`        | 删除一个或多个数据卷。只有当数据卷未被任何容器使用时才能删除。                                                         |
| `docker volume inspect` | `<volume_name>`       | `docker volume inspect my_data_volume`   | 显示一个或多个数据卷的详细信息。                                                                                       |
| `docker volume prune` |                       | `docker volume prune`                    | 删除所有未被任何容器引用的本地数据卷。通常用于清理废弃数据。                                                           |
|                   | `-f` (强制执行，不提示) | `docker volume prune -f`                 | `-f`: 强制执行，不进行确认提示。                                                                                       |

---

这份列表涵盖了 Docker 最常用和最基础的命令及其重要参数。掌握这些命令，你就能有效地管理 Docker 容器和镜像，并进行日常开发和运维工作。