# Dockerfile常见指令

Dockerfile 用于构建 Docker 镜像，由一系列指令和参数组成。以下是一些核心指令的详细说明：

### 1. `FROM`
- **作用**: 指定基础镜像，必须是 Dockerfile 的第一条指令。
- **格式**: `FROM <image>[:<tag>]`
- **示例**: `FROM ubuntu:20.04`

### 2. `RUN`
- **作用**: 在镜像构建过程中执行命令（如安装软件、创建目录等）。每条 `RUN` 指令都会在当前镜像之上创建一个新的层。
- **格式**: 
  - Shell 格式: `RUN <command>`
  - Exec 格式: `RUN ["executable", "param1", "param2"]`
- **示例**: `RUN apt-get update && apt-get install -y nginx`

### 3. `CMD`
- **作用**: 提供容器启动时默认执行的命令。一个 Dockerfile 中只能有一条 `CMD` 指令，如果有多条，只有最后一条生效。
- **格式**:
  - Exec 格式 (推荐): `CMD ["executable", "param1", "param2"]`
  - Shell 格式: `CMD command param1 param2`
- **注意**: `docker run` 命令如果指定了参数，会覆盖 `CMD` 的内容。

### 4. `ENTRYPOINT`
- **作用**: 配置容器启动时执行的命令，类似于 `CMD`，但它不会被 `docker run` 的参数轻易覆盖。相反，`docker run` 的参数会作为 `ENTRYPOINT` 的参数。
- **格式**:
  - Exec 格式 (推荐): `ENTRYPOINT ["executable", "param1", "param2"]`
- **示例**: `ENTRYPOINT ["nginx", "-g", "daemon off;"]`

### 5. `COPY` / `ADD`
- **作用**: 将宿主机的文���或目录复制到镜像中。
- **`COPY`**: 功能更纯粹，仅支持本地文件复制。
- **`ADD`**: 功能更强大，支持 URL 和自动解压 tar 文件，但因其行为不明确，推荐优先使用 `COPY`。
- **格式**: `COPY <src> <dest>`
- **示例**: `COPY ./app /app`

### 6. `WORKDIR`
- **作用**: 设置工作目录，后续的 `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, `ADD` 指令都会在该目录下执行。
- **格式**: `WORKDIR /path/to/workdir`
- **示例**: `WORKDIR /app`

### 7. `EXPOSE`
- **作用**: 声明容器运行时监听的端口，这仅作为元数据，并不会实际发布端口。
- **格式**: `EXPOSE <port>`
- **示例**: `EXPOSE 80`

### 8. `ENV`
- **作用**: 设置环境变量，这些变量在构建过程和容器运行时都可用。
- **格式**: `ENV <key>=<value>`
- **示例**: `ENV APP_VERSION=1.0`
