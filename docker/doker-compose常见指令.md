好的，这里是您需要的 `docker-compose.yml` 文件中常见指令（关键字）的详细表格，以及一个完整的 `docker-compose.yml` 示例。

`docker-compose` 的指令主要体现在其 YAML 配置文件 `docker-compose.yml` 中的各个关键字。

### Docker Compose (`docker-compose.yml`) 常见指令/关键字详解

下表列出了 `docker-compose.yml` 文件中最常用的一些顶级元素和服务配置关键字。

| 指令/关键字 (Key) | 作用 (Purpose) | 格式 (Format) | 示例 (Example) | 与相似指令的区别 (Differences from similar keys) |
| :--- | :--- | :--- | :--- | :--- |
| **`version`** (已弃用) | (历史用法) 定义 `docker-compose.yml` 文件的版本。**在 Compose V2 中已不再需要，会被忽略。** | `version: '3.8'` | `version: '3.9'` | 无。现代的 Compose specification 已不再使用此顶级元素。 |
| **`services`** | 定义所有的服务（容器）。这是 `docker-compose.yml` 的核心和必需部分。 | `services:`\<br\>  `[service_name]:` | `services:`\<br\>  `webapp:`\<br\>    `image: nginx` | 无。所有容器的定义都必须嵌套在 `services` 之下。 |
| **`networks`** | 定义供服务连接的自定义网络。 | `networks:`\<br\>  `[network_name]:` | `networks:`\<br\>  `my-network:` | 无。用于管理容器间的网络隔离和连接。 |
| **`volumes`** | 定义命名的卷 (Named Volumes)，用于数据持久化。 | `volumes:`\<br\>  `[volume_name]:` | `volumes:`\<br\>  `db-data:` | 无。用于跨容器生命周期管理持久化数据。 |
| **`build`** | 指定用于构建服务镜像的 Dockerfile 路径或构建上下文。 | `build: [path]` or `build:`\<br\>  `context: [path]`\<br\>  `dockerfile: [Dockerfile-name]`\<br\>  `args: ...` | `build: .` or `build:`\<br\>  `context: ./backend`\<br\>  `dockerfile: Dockerfile.prod` | **与 `image` 的区别**: `build` 从源代码构建镜像；`image` 直接从 Docker Hub 或其他镜像仓库拉取预构建的镜像。两者在一个服务中通常只使用一个。如果同时存在，`build` 优先，并会使用 `image` 指定的名称和标签来标记构建出的镜像。 |
| **`image`** | 指定服务使用的镜像名称和标签。 | `image: [repository]/[image_name]:[tag]` | `image: postgres:14-alpine` | **与 `build` 的区别**: 见上。`image` 用于使用现成的镜像，更快捷方便。 |
| **`ports`** | 将主机的端口映射到容器的端口。 | `ports:`\<br\>  `- "[HOST_PORT]:[CONTAINER_PORT]"` | `ports:`\<br\>  `- "8080:80"` | **与 `expose` 的区别**: `ports` 会将容器端口发布到主机，允许从外部访问。`expose` 仅在容器间的内部网络中暴露端口，而不发布到主机。 |
| **`expose`** | 仅在内部网络中暴露端口，供其他服务访问，但不对主机发布。 | `expose:`\<br\>  `- "[CONTAINER_PORT]"` | `expose:`\<br\>  `- "3000"` | **与 `ports` 的区别**: `expose` 不会做端口映射，主要用于服务间通信的声明。 |
| **`volumes`** (服务内) | 将主机路径或命名卷挂载到容器内。 | `volumes:`\<br\>  `- "[HOST_PATH or NAMED_VOLUME]:[CONTAINER_PATH]"` | `volumes:`\<br\>  `- ./config.json:/app/config.json`\<br\>  `- db-data:/var/lib/mysql` | 无。这是在服务级别定义数据挂载的方式，与顶级的 `volumes` 配合使用。 |
| **`environment`** | 设置容器内的环境变量。 | `environment:`\<br\>  `- VARIABLE=value`\<br\>or\<br\>  `VARIABLE: value` | `environment:`\<br\>  `DB_USER: user`\<br\>  `DB_PASS: secret` | **与 `env_file` 的区别**: `environment` 直接在 `docker-compose.yml` 文件中定义变量。`env_file` 从一个外部文件（如 `.env`）中读取变量，更利于保密和配置分离。 |
| **`env_file`** | 从文件中读取环境变量。 | `env_file:`\<br\>  `- [file_path]` | `env_file:`\<br\>  `- ./db.env` | **与 `environment` 的区别**: 见上。两者可以同时使用，`environment` 中定义的变量会覆盖 `env_file` 中的同名变量。 |
| **`depends_on`** | 定义服务间的启动依赖关系。被依赖的服务会先于当前服务启动。 | `depends_on:`\<br\>  `- [service_name]` | `depends_on:`\<br\>  `- db`\<br\>  `- redis` | **重要区别**: `depends_on` 只保证容器的启动顺序，不保证被依赖的服务内部的应用程序已经准备好接收请求。需要健康检查（`healthcheck`）来确保服务就绪。 |
| **`restart`** | 定义容器的重启策略，以应对容器退出的情况。 | `restart: [policy]` | `restart: always` or `restart: on-failure` | 无。常用策略包括 `no`（默认）、`always`（总是重启）、`on-failure`（仅在非零状态退出时重启）、`unless-stopped`（除非手动停止，否则总是重启）。 |
| **`command`** | 覆盖 Dockerfile 中的默认 `CMD` 指令。 | `command: [command]` or `command: ["executable", "param1"]` | `command: "python app.py"` | **与 `entrypoint` 的区别**: `command` 覆盖 `Dockerfile` 中的 `CMD`。`entrypoint` 覆盖 `Dockerfile` 中的 `ENTRYPOINT`。`command` 的内容可以作为 `entrypoint` 的参数。 |
| **`entrypoint`** | 覆盖 Dockerfile 中的默认 `ENTRYPOINT` 指令。 | `entrypoint: [command]` or `entrypoint: ["executable", "param1"]` | `entrypoint: /docker-entrypoint.sh` | **与 `command` 的区别**: `entrypoint` 是容器启动时执行的主命令，而 `command` 通常作为其参数。修改 `entrypoint` 的场景比 `command` 少。 |
| **`healthcheck`** | 定义如何检查服务（容器）是否“健康”。`depends_on` 可以结合 `healthcheck` 来等待服务真正可用。 | `healthcheck:`\<br\>  `test: ["CMD", "curl", "-f", "http://localhost"]`\<br\>  `interval: 1m30s`\<br\>  `timeout: 10s`\<br\>  `retries: 3` | `healthcheck:`\<br\>  `test: ["CMD-SHELL", "pg_isready -U postgres"]`\<br\>  `interval: 5s` | 无。这是实现可靠服务依赖关系的关键。 |

-----

### 完整 `docker-compose.yml` 示例

这是一个包含 Web 应用、数据库和缓存服务的典型多容器应用示例。

**场景**: 一个 Python Web 应用 (Flask)，使用 PostgreSQL 作为数据库，Redis 作为缓存。

**项目结构**:

```
.
├── docker-compose.yml
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── app.py
└── .env
```

**`.env` 文件内容 (用于存放敏感信息):**

```env
# PostgreSQL Credentials
POSTGRES_DB=mydb
POSTGRES_USER=myuser
POSTGRES_PASSWORD=mysecretpassword

# Redis Password
REDIS_PASSWORD=myredispassword
```

**`docker-compose.yml` 文件内容:**

```yaml
# 使用 Compose file format 3.9
# version 字段在 V2 中已弃用，但为了兼容性或明确性有时仍会保留
# version: '3.9'

# 定义所有服务
services:
  # Web 应用服务
  backend:
    # 从当前目录下的 backend 文件夹构建镜像
    build: ./backend
    # 设置容器名称，方便识别
    container_name: flask_app
    # 将主机的 5000 端口映射到容器的 5000 端口
    ports:
      - "5000:5000"
    # 挂载当前 backend 目录到容器的 /app 目录，方便代码热更新
    volumes:
      - ./backend:/app
    # 从 .env 文件加载环境变量
    env_file:
      - .env
    # 设置额外的环境变量
    environment:
      - FLASK_ENV=development
      - DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@db:5432/${POSTGRES_DB}
      - REDIS_URL=redis://:${REDIS_PASSWORD}@redis:6379
    # 定义启动依赖，backend 会在 db 和 redis 启动后才启动
    depends_on:
      db:
        # 等待 db 服务达到 healthy 状态后才启动 backend
        condition: service_healthy
      redis:
        # 等待 redis 服务达到 healthy 状态后才启动 backend
        condition: service_healthy
    # 将此服务连接到 my-network 网络
    networks:
      - my-network

  # 数据库服务
  db:
    # 直接使用官方的 PostgreSQL 镜像
    image: postgres:14-alpine
    container_name: postgres_db
    # 从 .env 文件加载数据库配置
    env_file:
      - .env
    # 使用命名卷来持久化数据库数据
    volumes:
      - db-data:/var/lib/postgresql/data
    # 健康检查，确保数据库服务真正可用
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
      interval: 5s
      timeout: 5s
      retries: 5
    # 设置重启策略
    restart: unless-stopped
    networks:
      - my-network

  # 缓存服务
  redis:
    # 使用官方的 Redis 镜像
    image: redis:7-alpine
    container_name: redis_cache
    # 设置 Redis 密码，通过 command 指令
    command: redis-server --requirepass ${REDIS_PASSWORD}
    # 健康检查
    healthcheck:
      test: ["CMD", "redis-cli", "-a", "${REDIS_PASSWORD}", "ping"]
      interval: 5s
      timeout: 5s
      retries: 5
    restart: unless-stopped
    networks:
      - my-network

# 顶级关键字，定义网络
networks:
  my-network:
    # 使用 bridge 驱动（默认）
    driver: bridge

# 顶级关键字，定义命名卷
volumes:
  # 创建一个名为 db-data 的命名卷
  db-data:
    driver: local
```

**如何使用这个 `docker-compose.yml`:**

1.  创建上述文件结构和内容。
2.  在项目根目录下（与 `docker-compose.yml` 同级）打开终端。
3.  运行以下命令来构建并启动所有服务：
    ```bash
    docker-compose up --build -d
    ```
      * `--build`: 强制重新构建镜像（在初次运行时或代码有更新时使用）。
      * `-d`: 在后台（detached mode）运行容器。
4.  查看正在运行的服务：
    ```bash
    docker-compose ps
    ```
5.  停止并移除所有服务、网络和卷：
    ```bash
    docker-compose down -v
    ```
      * `-v`: 同时移除在 `volumes` 中定义的命名卷。