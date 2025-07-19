## Docker Compose 命令详解

Docker Compose 是一个用于定义和运行多容器 Docker 应用程序的工具。通过一个 YAML 文件来配置应用程序的服务，然后使用一个命令来启动和管理所有服务。下面是 Docker Compose 的常用命令分类整理，并附带重要参数详解和示例。

-----

### 一、项目生命周期管理命令

| 命令 | 描述 | 重要参数详解 | 使用示例 |
| :--- | :--- | :----------- | :------- |
| `up` | **启动并管理整个 Compose 项目**：该命令会根据 `docker-compose.yml` 文件定义的服务，**构建（如果需要）**、**创建**、**启动**并**连接**到所有服务容器。如果服务容器已经存在，Compose 会检测配置变化，并**停止并重建**受影响的容器，然后重新启动它们，确保所有服务都处于最新配置状态。它还会创建 Compose 文件中定义的网络和卷。 | **`-d`, `--detach`**: **后台运行容器**。这是最常用的参数，它会**将所有服务容器放到后台运行**，并释放你的终端。这样你就可以在服务运行的同时继续使用终端执行其他命令。<br>**`--build`**: **强制重新构建镜像**。即使 Compose 认为某个服务的镜像已经是最新状态，添加此参数也会强制 Docker 重新执行 `Dockerfile` 中的构建步骤来**重新生成**该服务的镜像。这在 `Dockerfile` 或其依赖的文件发生变化时非常有用。<br>**`--force-recreate`**: **强制重新创建容器**。即使 Compose 检测到服务的配置没有改变，也会强制**停止**并**重新创建**该服务的所有容器。这对于解决一些容器运行时可能出现的奇怪问题很有帮助，确保容器从一个全新的状态启动。<br>**`--remove-orphans`**: **删除孤儿容器**。如果你的 `docker-compose.yml` 文件发生了变化，移除了某个服务，但该服务的容器仍然在运行，这个参数会**自动检测并移除**这些不再被 Compose 文件定义的服务容器（即"孤儿"容器）。 | `docker compose up -d`<br>`docker compose up --build`<br>`docker compose up --force-recreate web` |
| `down` | **停止并移除 Compose 项目**：此命令会优雅地**停止**并**移除** `docker-compose.yml` 文件中定义的所有服务容器。除了容器，它还会**移除** Compose 项目创建的默认网络以及通过 Compose 文件声明的匿名卷（如果未指定 `-v` 参数）。这是清理项目环境的常用命令。 | **`-v`, `--volumes`**: **移除卷**。除了停止并移除服务容器和默认网络，添加此参数还会**移除**Compose 文件中声明的**匿名卷和已命名卷**。在开发环境中，如果你想彻底清除所有相关数据，包括数据库数据卷等，这个参数非常有用。<br>**`--rmi all`**: **移除所有镜像**。这个参数会**移除**所有由 Compose 文件定义的（通过 `build` 指令构建的）以及由其依赖服务所使用的镜像。慎用此参数，因为它会删除你的本地镜像。<br>**`--remove-orphans`**: **删除孤儿容器**。与 `up` 命令中的作用相同，确保在 `down` 操作时也清理掉不再被 Compose 文件定义的服务容器。 | `docker compose down`<br>`docker compose down -v`<br>`docker compose down --rmi all` |
| `start` | **启动已停止的服务**：该命令用于**启动**之前已经通过 `docker compose stop` 命令停止但尚未移除的服务容器。它不会重新创建容器，只是让它们从停止状态恢复运行。 | | `docker compose start`<br>`docker compose start db` |
| `stop` | **停止运行中的服务**：此命令会**停止**正在运行的服务容器，但**不会移除**它们。这意味着容器的状态（包括数据）会得以保留，可以通过 `docker compose start` 再次启动。 | | `docker compose stop`<br>`docker compose stop web` |
| `restart` | **重启服务**：该命令会**停止**然后**重新启动**指定的服务。这对于应用配置更新后需要快速重启服务而无需重建容器的情况很有用。 | | `docker compose restart`<br>`docker compose restart web` |
| `rm` | **移除已停止的服务容器**：用于**移除**一个或多个已经停止的服务容器。这通常在 `docker compose down` 命令不够彻底，或者只需要移除特定已停止服务时使用。 | | `docker compose rm`<br>`docker compose rm -f web` |

-----

### 二、构建与调试命令

| 命令 | 描述 | 重要参数详解 | 使用示例 |
| :--- | :--- | :----------- | :------- |
| `build` | **构建或重建服务镜像**：该命令会根据 `Dockerfile` 来**构建**或**重建**指定服务的 Docker 镜像。它会读取 `Dockerfile` 中的指令，并创建相应的镜像层。通常在 `docker compose up` 之前手动运行，或者与 `docker compose up --build` 一起使用。 | **`--no-cache`**: **构建镜像时不使用缓存**。默认情况下，Docker 在构建镜像时会尝试利用缓存层来加速构建过程。使用此参数会**禁用缓存**，强制 Docker 从头开始执行 `Dockerfile` 中的所有步骤。这在调试 `Dockerfile` 问题或确保获得最新构建时很有用。 | `docker compose build`<br>`docker compose build --no-cache web` |
| `exec` | **在运行中的容器中执行命令**：允许你在一个**正在运行的服务容器**中执行任意命令。这对于进入容器进行调试、检查文件、运行脚本等操作非常有用。 | **`-it`**: **交互式伪 TTY**。这是一个常用的组合参数：<br> - **`-i`, `--interactive`**: **保持 STDIN 打开**，即使没有连接到终端。这允许你在容器内部进行输入。<br> - **`-t`, `--tty`**: **分配一个伪 TTY**。这会创建一个虚拟终端，使你能够像在常规命令行界面一样与容器内部的进程进行交互（例如，使用方向键、Tab 补全等）。 | `docker compose exec web bash`<br>`docker compose exec -it db psql -U user` |
| `logs` | **显示服务日志输出**：获取并显示一个或多个服务的标准输出和标准错误日志。这对于监控应用行为、调试问题和查看运行时信息至关重要。 | **`-f`, `--follow`**: **跟踪日志输出**。该参数会**持续显示**新的日志条目，实时地将服务产生的日志输出到你的终端。这对于监控长时间运行的服务或在调试时观察实时行为非常有用。<br>**`--tail N`**: **显示最近的 N 行日志**。该参数用于**限制显示的日志行数**，只显示指定服务或所有服务最新的 `N` 行日志。 | `docker compose logs -f`<br>`docker compose logs --tail 50 web` |

-----

### 三、信息与配置命令

| 命令 | 描述 | 使用示例 |
| :--- | :--- | :------- |
| `ps` | **列出服务容器状态**：显示 Compose 项目中所有服务容器的详细信息，包括它们的名称、命令、状态（运行中、已停止、退出等）、端口映射以及其他有用信息。 | `docker compose ps`<br>`docker compose ps -a` |
| `ls` | **列出 Compose 项目**：列出当前或指定目录下的 Compose 项目。在新版本的 Docker Compose 中，这个命令用于查看所有正在运行或已停止的 Compose 项目。 | `docker compose ls` |
| `config` | **验证并显示 Compose 文件配置**：该命令会**验证** `docker-compose.yml` 文件的语法和结构是否正确。如果文件有效，它会**显示**解析后的配置，这对于调试 Compose 文件或理解其最终配置很有帮助。 | `docker compose config`<br>`docker compose config --services` |
| `images` | **列出服务使用的镜像**：显示 Compose 项目中所有服务所使用的 Docker 镜像列表，包括镜像名称和ID。 | `docker compose images` |
| `port` | **打印服务指定端口的公共端口**：查找并打印指定服务上特定端口映射到主机上的公共端口。这在你需要知道动态分配的端口时很有用。 | `docker compose port web 80` |
| `top` | **显示服务运行进程**：类似于 Linux 的 `top` 命令，它会显示指定服务容器内正在运行的进程信息，包括进程ID、CPU使用率和内存使用率等。 | `docker compose top db` |

-----

### 四、开发工具与高级命令

| 命令 | 描述 | 使用示例 |
| :--- | :--- | :------- |
| `run` | **在一次性容器中运行命令**：在新的、**一次性**的服务容器中执行一个命令。与 `exec` 不同，`run` 会创建一个全新的容器，并且在命令执行完毕后通常会退出。这对于运行一次性任务、数据库迁移或测试脚本非常有用。 | `docker compose run web python manage.py migrate` |
| `pull` | **拉取服务依赖的镜像**：从配置的镜像仓库（默认为 Docker Hub）**拉取**Compose 文件中所有服务或指定服务所依赖的 Docker 镜像。 | `docker compose pull`<br>`docker compose pull redis` |
| `push` | **推送服务依赖的镜像**：将 Compose 文件中所有服务或指定服务所构建或使用的本地镜像**推送**到配置的镜像仓库。 | `docker compose push` |
| `convert` | **转换 Compose 文件格式**：将 `docker-compose.yml` 文件转换为其他格式，例如 Kubernetes 清单（YAML 文件）。这有助于在不同容器编排平台之间迁移应用。 | `docker compose convert -o kubernetes.yaml` |
| `events` | **实时显示容器事件**：持续**显示**来自 Compose 项目中所有容器的实时事件流，如容器的启动、停止、重启等。这对于监控容器生命周期事件很有帮助。 | `docker compose events --json` |
| `kill` | **强制停止运行中的服务容器**：使用 `SIGKILL` 信号**强制停止**一个或多个正在运行的服务容器。与 `stop` 不同，`kill` 不会等待容器正常关闭，可能会导致数据丢失或状态不一致。通常在服务无法正常停止时作为最后手段。 | `docker compose kill -s SIGKILL web` |
| `pause` | **暂停服务**：**暂停**一个或多个正在运行的服务容器中的所有进程。容器的状态会被冻结，但不会被停止。 | `docker compose pause db` |
| `unpause` | **取消暂停服务**：**取消暂停**之前通过 `docker compose pause` 命令暂停的服务容器，使其恢复运行。 | `docker compose unpause db` |

-----

### 示例

假设你有一个 `docker-compose.yml` 文件如下：

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "80:80"
    volumes:
      - ./app:/usr/share/nginx/html
    depends_on:
      - db
  db:
    image: postgres:13
    environment:
      POSTGRES_DB: mydatabase
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

#### 1\. 启动所有服务并在后台运行

```bash
docker compose up -d
```

这个命令会根据 `docker-compose.yml` 文件定义的服务，构建（如果需要）`web` 服务镜像，然后启动 `db` 和 `web` 服务容器，并将它们置于后台运行。

#### 2\. 查看正在运行的服务

```bash
docker compose ps
```

此命令会显示 `web` 和 `db` 服务容器的当前状态、端口映射和运行时间等信息。

#### 3\. 查看 `web` 服务的实时日志

```bash
docker compose logs -f web
```

这个命令会持续输出 `web` 服务（假设是一个 Nginx 服务器）的访问日志和错误日志，让你实时监控其运行情况。

#### 4\. 进入 `web` 服务的容器内部，执行 Bash shell

```bash
docker compose exec -it web bash
```

你将获得一个 `web` 服务容器内部的 Bash shell，可以在其中执行各种命令，例如查看文件、修改配置（临时性）或运行诊断工具。

#### 5\. 停止并移除所有服务、网络和数据卷

```bash
docker compose down -v
```

这个命令会停止并删除 `web` 和 `db` 容器，移除由 Compose 创建的网络，并且最重要的是，会删除 `db_data` 这个持久化数据卷，从而清除数据库的所有数据。