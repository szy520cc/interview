### Dockerfile 常见指令详解

下表列出了 Dockerfile 中最常用的一些指令，并对它们的用途、格式、示例以及与相似命令的区别进行了说明。

| 命令 (Instruction) | 作用 (Purpose) | 格式 (Format) | 示例 (Example) | 与相似命令的区别 (Differences from similar commands) |
| :--- | :--- | :--- | :--- | :--- |
| **`FROM`** | 指定基础镜像。Dockerfile 的第一条指令必须是 `FROM`。 | `FROM <image>`<br>`FROM <image>:<tag>`<br>`FROM <image>@<digest>` | `FROM ubuntu:22.04` | 无相似命令。这是构建任何 Docker 镜像的起点。 |
| **`RUN`** | 在镜像构建过程中执行命令。通常用于安装软件包、创建目录等。每条 `RUN` 指令都会在当前镜像顶部创建一个新的层。 | `RUN <command>` (shell 格式)<br>`RUN ["executable", "param1", "param2"]` (exec 格式) | `RUN apt-get update && apt-get install -y nginx`<br>`RUN ["/bin/bash", "-c", "echo hello"]` | **与 `CMD`/`ENTRYPOINT` 的区别**: `RUN` 在构建镜像时 (build time) 执行并提交到镜像中；`CMD` 和 `ENTRYPOINT` 在容器启动时 (run time) 执行。 |
| **`CMD`** | 为启动的容器提供默认的执行命令。一个 Dockerfile 中只能有一条 `CMD` 指令，如果有多条，只有最后一条生效。 | `CMD ["executable","param1","param2"]` (exec 格式, **推荐**)<br>`CMD ["param1","param2"]` (作为 `ENTRYPOINT` 的默认参数)<br>`CMD command param1 param2` (shell 格式) | `CMD ["nginx", "-g", "daemon off;"]` | **与 `ENTRYPOINT` 的区别**: `CMD` 的命令可以被 `docker run` 命令后面的参数覆盖。`ENTRYPOINT` 的命令不会被覆盖，`docker run` 的参数会作为 `ENTRYPOINT` 的参数。两者可以结合使用。 |
| **`ENTRYPOINT`** | 配置容器启动后执行的命令。`docker run` 命令行中提供的参数会被当作 `ENTRYPOINT` 指定命令的参数。 | `ENTRYPOINT ["executable", "param1", "param2"]` (exec 格式, **推荐**)<br>`ENTRYPOINT command param1 param2` (shell 格式) | `ENTRYPOINT ["java", "-jar", "app.jar"]` | **与 `CMD` 的区别**: `ENTRYPOINT` 不会被 `docker run` 的参数轻易覆盖，而是将这些参数接收为自己的参数，使得容器可以像一个可执行文件一样使用。`CMD` 更适合作为 `ENTRYPOINT` 的默认参数或可被覆盖的默认命令。 |
| **`COPY`** | 从构建上下文（通常是 Dockerfile 所在的目录）复制文件或目录到镜像的文件系统中。 | `COPY [--chown=<user>:<group>] <src>... <dest>` | `COPY . /app` | **与 `ADD` 的区别**: `COPY` 功能更单一，只负责复制。`ADD` 功能更强大，除了复制外，还支持解压本地 tar 文件和下载远程 URL 文件。官方推荐**优先使用 `COPY`**，因为它的行为更可预测。 |
| **`ADD`** | 功能与 `COPY` 类似，但增加了两个特性：1. 如果源文件是本地的 tar 压缩文件，会自动解压到目标路径。2. 源文件可以是 URL。 | `ADD [--chown=<user>:<group>] <src>... <dest>` | `ADD http://example.com/big.tar.xz /usr/src/things`<br>`ADD rootfs.tar.gz /` | **与 `COPY` 的区别**: 由于 `ADD` 的自动解压和远程下载功能，其行为可能不如 `COPY` 透明。除非明确需要 `ADD` 的特殊功能，否则建议使用 `COPY`。 |
| **`WORKDIR`** | 设置工作目录。后续的 `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, `ADD` 指令都会在这个目录下执行。 | `WORKDIR /path/to/workdir` | `WORKDIR /app` | 无相似命令。它会影响后续指令的执行路径。如果目录不存在，`WORKDIR` 会自动创建它。 |
| **`EXPOSE`** | 声明容器在运行时监听的端口。这只是一个元数据声明，并不会实际发布端口。 | `EXPOSE <port> [<port>/<protocol>...]` | `EXPOSE 80/tcp`<br>`EXPOSE 8080` | 无相似命令。`EXPOSE` 仅仅是声明，实际的端口映射需要在 `docker run` 时使用 `-p` 或 `-P` 参数。 |
| **`ENV`** | 设置环境变量。这些环境变量在镜像构建过程和容器运行期间都可用。 | `ENV <key>=<value> ...` | `ENV JAVA_HOME /usr/lib/jvm/java-11-openjdk` | 无相似命令。是设置持久化环境变量的标准方式。 |
| **`ARG`** | 定义构建时变量。这些变量只在 Dockerfile 构建过程中有效，容器运行时不可用。可以通过 `docker build --build-arg <varname>=<value>` 来传递。 | `ARG <name>[=<default value>]` | `ARG APP_VERSION=1.0` | **与 `ENV` 的区别**: `ARG` 是构建时变量 (build-time)，`ENV` 是运行时环境变量 (run-time)。`ARG` 的作用域仅限于 `docker build` 过程。 |
| **`VOLUME`** | 创建一个可以绕过联合文件系统的挂载点，用于持久化数据。 | `VOLUME ["/path/to/volume"]` | `VOLUME ["/var/lib/mysql"]` | 无相似命令。用于将容器内的特定路径标记为卷，以便存储数据库文件、日志等需要持久化的数据。 |
| **`USER`** | 指定运行后续 `RUN`, `CMD`, `ENTRYPOINT` 指令时使用的用户名或 UID。 | `USER <user>[:<group>]` or `USER <UID>[:<GID>]` | `USER node` | 无相似命令。为了安全，推荐创建一个非 root 用户并使用 `USER` 指令切换，避免在容器中以 root 身份运行应用。 |

-----

### 完整 Dockerfile 示例

这是一个构建 Node.js Web 应用程序的典型 Dockerfile 示例，它利用了多阶段构建（Multi-stage build）来减小最终镜像的体积并提高安全性。

```dockerfile
# =================================================================
# 第一阶段：构建/编译阶段 (Builder Stage)
# 使用一个包含完整构建工具的镜像
# =================================================================
FROM node:18-alpine AS builder

# 设置工作目录
WORKDIR /app

# 复制 package.json 和 package-lock.json
# 利用 Docker 的缓存机制，只有当这些文件变化时，才会重新执行 npm install
COPY package*.json ./

# 安装项目依赖
RUN npm install

# 复制所有源代码到工作目录
COPY . .

# （可选）如果你的项目有构建步骤，例如 TypeScript 编译或前端打包
RUN npm run build

# =================================================================
# 第二阶段：生产阶段 (Production Stage)
# 使用一个轻量级的基础镜像来运行应用
# =================================================================
FROM node:18-alpine

# 再次设置工作目录
WORKDIR /app

# 创建一个非 root 用户来运行应用，增强安全性
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# 从 'builder' 阶段复制必要的构建产物和依赖
# --chown=appuser:appgroup 确保复制过来的文件属于我们创建的非 root 用户
COPY --from=builder --chown=appuser:appgroup /app/node_modules ./node_modules
COPY --from=builder --chown=appuser:appgroup /app/package*.json ./
COPY --from=builder --chown=appuser:appgroup /app/dist ./dist  # 假设构建产物在 dist 目录
# 如果没有构建步骤，则复制源码
# COPY --from=builder --chown=appuser:appgroup /app/src ./src

# 设置环境变量
ENV NODE_ENV=production
ENV PORT=3000

# 暴露应用监听的端口
EXPOSE 3000

# 定义容器启动时执行的命令
# 假设应用的入口文件是 dist/index.js
CMD ["node", "dist/index.js"]
```

**如何使用这个 Dockerfile:**

1.  将上述内容保存为名为 `Dockerfile` 的文件，并放置在你的 Node.js 项目根目录下。
2.  确保你的项目有 `package.json` 文件。
3.  在项目根目录下打开终端，运行构建命令：
    ```bash
    docker build -t my-node-app .
    ```
4.  构建成功后，运行容器：
    ```bash
    docker run -d -p 8080:3000 --name my-running-app my-node-app
    ```

现在，你的 Node.js 应用应该已经在容器中运行，并且可以通过访问 `http://localhost:8080` 来访问。