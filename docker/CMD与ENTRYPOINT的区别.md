# CMD与ENTRYPOINT的区别

`CMD` 和 `ENTRYPOINT` 都是 Dockerfile 中用于指定容器启动时默认执行命令的指令，但它们在使用场景和行为上存在关键区别。

### 核心区别总结

| 指令         | 主要用途                                       | 是否容易被覆盖                                     | 与 `docker run` 参数的关系                               |
| :----------- | :--------------------------------------------- | :------------------------------------------------- | :------------------------------------------------------- |
| **`CMD`**    | 提供容器的**默认**执行命令，主要用于**可变**的、易于替换的场景。 | **容易**。`docker run` 后面跟的任何命令都会完全覆盖 `CMD`。 | `docker run` 的命令直接替换整个 `CMD` 指令。             |
| **`ENTRYPOINT`** | 定义容器的**主要**执行程序，主要用于**固定**的、不易改变的场景。 | **不容易**。`docker run` 的参数会作为 `ENTRYPOINT` 的**参数**追加。 | `docker run` 的命令作为 `ENTRYPOINT` 的参数。            |

---

### `CMD` 的行为

`CMD` 的主要目的是为执行中的容器提供默认值。这些默认值可以包括一个可执行文件，也可以省略可执行文件（此时必须指定 `ENTRYPOINT`）。

**场景1: `docker run` 不带参数**
- `Dockerfile`: `CMD ["/bin/echo", "Hello World"]`
- `docker run <image>`
- **结果**: 容器输出 `Hello World`。

**场景2: `docker run` 带参数**
- `Dockerfile`: `CMD ["/bin/echo", "Hello World"]`
- `docker run <image> /bin/ls -l`
- **结果**: `CMD` 被完全覆盖，容器执行 `ls -l` 命令，列出根目录文件。

### `ENTRYPOINT` 的行为

`ENTRYPOINT` 将容器配置为像一个可执行程序一样运行。

**场景1: `docker run` 不带参数**
- `Dockerfile`: `ENTRYPOINT ["/bin/echo", "Hello"]`
- `docker run <image>`
- **结果**: 容器输出 `Hello`。

**场景2: `docker run` 带参数**
- `Dockerfile`: `ENTRYPOINT ["/bin/echo", "Hello"]`
- `docker run <image> World`
- **结果**: `World` 作为参数追加到 `ENTRYPOINT` 后面，��器执行 `/bin/echo Hello World`，输出 `Hello World`。

### `CMD` 和 `ENTRYPOINT` 结合使用

这是最强大和最推荐的使用方式。当它们结合使用时，`CMD` 的内容会作为 `ENTRYPOINT` 的**默认参数**。

**场景1: `docker run` 不带参数**
- `Dockerfile`: 
  ```dockerfile
  ENTRYPOINT ["/bin/ls"]
  CMD ["-l", "/"]
  ```
- `docker run <image>`
- **结果**: `CMD` 的内容作为 `ENTRYPOINT` 的默认参数，容器执行 `ls -l /`。

**场景2: `docker run` 带参数**
- `Dockerfile`: 
  ```dockerfile
  ENTRYPOINT ["/bin/ls"]
  CMD ["-l", "/"]
  ```
- `docker run <image> -a /etc`
- **结果**: `docker run` 的参数 `-a /etc` 覆盖了 `CMD` 的内容，并作为 `ENTRYPOINT` 的参数。容器执行 `ls -a /etc`。

### 最佳实践

- 使用 `ENTRYPOINT` 来设置镜像的主要命令，使其行为固定。
- 使用 `CMD` 来为 `ENTRYPOINT` 提供默认的、可被用户轻易覆盖的参数。

这种模式非常适合创建工具型镜像，例如：
`Dockerfile`:
```dockerfile
ENTRYPOINT ["git"]
CMD ["--help"]
```
- `docker run my-git-image` -> 执行 `git --help`
- `docker run my-git-image clone <repo_url>` -> 执行 `git clone <repo_url>`
