# 后端面试核心知识库

本项目用于整理和复习后端面试常见知识点，重点覆盖 Docker、Git、MySQL、Redis 及 Elasticsearch 相关高频考点，内容深入且贴近实战，适合面试准备和日常查阅。

## 目录结构

### 🐳 Docker

-   **`CMD与ENTRYPOINT的区别.md`**: 深入解析 `CMD` 与 `ENTRYPOINT` 在容器启动命令中的核心差异与结合使用的最佳实践。
-   **`Dockerfile多阶段构建.md`**: 阐述多阶段构建如何分离构建与运行环境，以优化镜像大小和安全性。
-   **`Dockerfile常见指令.md`**: 详解 `FROM`, `RUN`, `COPY`, `CMD`, `ENTRYPOINT` 等 Dockerfile 常用指令的用途和区别。
-   **`Docker基础命令.md`**: 汇总容器生命周期、镜像管理、网络和数据卷等 Docker 核心基础命令。
-   **`doker-compose常见指令.md`**: 系统整理 Docker Compose 在项目生命周期管理、构建与调试中的常用命令。
-   **`容器与镜像的区别.md`**: 从静态与动态、层级结构等角度，深刻剖析容器与镜像的本质区别。
-   **`常见面试题-gemini.md`**: 由 Gemini AI 生成的 Docker 高频面试题及详细解析。
-   **`常见面试题-grok.md`**: 由 Grok AI 生成的 Docker 高频面试题及详细解析。
-   **`网络驱动.md`**: 介绍 Bridge, Host, Overlay, Macvlan 等 Docker 网络驱动的工作原理与适用场景。

### 🐙 Git

-   **`git高频面试题.md`**: 全面覆盖 Git 基础、分支管理、远程协作及问题排查等高频面试题。

### 🐘 MySQL

-   **`sql优化.md`**: 汇总深度分页、索引失效、关联查询等10大经典SQL优化场景及解决方案。
-   **`事务.md`**: 深入讲解事务的ACID特性、四种隔离级别、死锁问题及其避免策略，并包含PHP代码实践。
-   **`深度分页.md`**: 探讨大数据量下的分页查询优化，系统性介绍基于游标、子查询、分段查询和缓存的多种高性能分页方案。
-   **`锁/元数据锁.md`**: 解析MySQL的元数据锁（MDL）及其与DDL操作的关系，帮助理解并避免长事务引发的DDL阻塞问题。

### 🚀 Redis

-   **`AOF重写.md`**: 阐述AOF重写机制如何通过`fork`子进程工作，以实现AOF文件的压缩。
-   **`mysql与redis在原子性上的区别.md`**: 深度对比MySQL的ACID事务与Redis的原子操作（`MULTI`/`EXEC`、Lua脚本）在实现机制上的核心差异。
-   **`unlink.md`**: 说明`UNLINK`命令如何通过异步方式删除大键，避免阻塞主线程。
-   **`五种数据类型.md`**: 详细介绍String, Hash, List, Set, Zset的定义、常用命令和丰富的应用场景。
-   **`单线程.md`**: 深刻解释Redis单线程模型为何高效，及其背后的I/O多路复用技术，并介绍了后台线程的作用。
-   **`混合持久化.md`**: 介绍Redis 4.0引入的RDB-AOF混合持久化模式，如何兼顾RDB的快速恢复和AOF的数据安全。
-   **`过期删除策略.md`**: 分析惰性删除、定期删除和内存淘汰这三种过期键删除策略如何协同工作。
-   **`雪崩-击穿-穿透.md`**: 系统性总结缓存三大问题的定义、出现场景及对应的解决方案，是构建高可用缓存系统的必备知识。

### 🔍 Elasticsearch

-   **`复习笔记.md`**: 包含 ES 核心概念、倒排索引、Mapping、数据类型、写入与搜索流程、Master 选举等全面复习笔记。
-   **`高频面试题.md`**: 汇总 ES 核心原理、技术点对比（如 `text` vs `keyword`）、深分页、性能调优等高频面试题。
-   **`Elasticsearch索引数据流程.md`**: 详细描述从客户端请求到数据持久化的完整索引流程，包含协调节点、主分片和副本分片的协作机制。
-   **`Mapping.md`**: 对比 Elasticsearch Mapping 与 MySQL 表结构，深入讲解其在字段定义、索引机制和查询方式上的核心���异。
-   **`query与filter区别.md`**: 从相关性评分、缓存机制、性能开销等维度，深度剖析 `query` 与 `filter` 的区别与最佳实践。
-   **`term与match区别.md`**: 详解 `term`（精确匹配）与 `match`（全文检索）在分词行为、适用字段和查询逻辑上的本质不同。
-   **`text与keyword区别.md`**: 深入对比 `text` 和 `keyword` 数据类型在分词、索引、聚合及排序等方面的核心差异与适用场景。

## 使用说明

可直接阅读各 Markdown 文档，快速掌握面试重点知识。

---

如有补充建议，欢迎完善文档内容。
