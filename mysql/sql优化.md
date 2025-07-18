
# 🛠️ 经典 SQL 优化场景与解决方案

在日常工作中，SQL 优化是提升系统性能的关键一环。以下汇总了工作中常见的 SQL 优化场景及其解决方案，希望能帮助您更好地进行数据库性能调优。

---

## 1. 深度分页优化

**问题场景**：  
电商系统商品列表，当翻页到几万页时查询速度显著变慢。

**慢查询**：

```sql
SELECT * FROM products ORDER BY id LIMIT 50000, 20;
```

**优化方案**：  
通过子查询定位起始 `id`，避免全表扫描大量偏移量。

```sql
SELECT * FROM products
WHERE id >= (
  SELECT id FROM products ORDER BY id LIMIT 50000, 1
)
ORDER BY id LIMIT 20;
```

**优化效果**：  
查询时间可从 **3 秒** 优化到 **50 毫秒**。

---

## 2. 索引失效优化

**问题场景**：  
用户搜索功能中，前缀模糊查询导致索引失效。

**慢查询**：

```sql
SELECT * FROM users WHERE username LIKE '%admin%';
```

**优化方案**：  
使用全文索引（FULLTEXT）代替模糊匹配。

```sql
ALTER TABLE users ADD FULLTEXT(username);
SELECT * FROM users WHERE MATCH(username) AGAINST('admin');
```

**优化效果**：  
搜索时间可从 **2 秒** 优化到 **100 毫秒**。

---

## 3. 关联查询优化

**问题场景**：  
订单详情页面涉及多表关联，查询慢。

**慢查询**：

```sql
SELECT o.*, u.username, p.product_name
FROM orders o
LEFT JOIN users u ON o.user_id = u.id
LEFT JOIN products p ON o.product_id = p.id
WHERE o.create_time >= '2024-01-01';
```

**优化方案**：  
为 JOIN 条件和 WHERE 条件字段添加组合索引。

```sql
CREATE INDEX idx_orders_time_user ON orders(create_time, user_id);
CREATE INDEX idx_orders_time_product ON orders(create_time, product_id);
```

---

## 4. 子查询优化

**问题场景**：  
查询每个分类下最新的商品。

**慢查询**：

```sql
SELECT * FROM products p1
WHERE p1.create_time = (
  SELECT MAX(create_time)
  FROM products p2
  WHERE p2.category_id = p1.category_id
);
```

**优化方案**：  
使用窗口函数（如 `ROW_NUMBER()`）提高效率。

```sql
SELECT * FROM (
  SELECT *,
         ROW_NUMBER() OVER (PARTITION BY category_id ORDER BY create_time DESC) as rn
  FROM products
) t WHERE rn = 1;
```

---

## 5. 大数据量统计优化

**问题场景**：  
统计报表中，`COUNT(*)` 查询慢。

**慢查询**：

```sql
SELECT COUNT(*) FROM orders WHERE status = 'completed';
```

**优化方案**：

- 使用覆盖索引：

```sql
CREATE INDEX idx_orders_status ON orders(status);
```

- 使用估算（非精确）：

```sql
SELECT table_rows
FROM information_schema.tables
WHERE table_name = 'orders';
```

**优化效果**：  
统计时间从 **30 秒** 降至 **3 秒**。

---

## 6. 批量操作优化

**问题场景**：  
单条插入数据性能低。

**慢操作**：

```sql
INSERT INTO logs (user_id, action, time) VALUES (1, 'login', NOW());
INSERT INTO logs (user_id, action, time) VALUES (2, 'logout', NOW());
```

**优化方案**：  
使用批量插入：

```sql
INSERT INTO logs (user_id, action, time) VALUES
(1, 'login', NOW()),
(2, 'logout', NOW()),
(3, 'view', NOW());
```

---

## 7. 时间范围查询优化

**问题场景**：  
对日期字段使用函数导致索引失效。

**慢查询**：

```sql
SELECT * FROM orders WHERE DATE(create_time) = '2024-01-01';
```

**优化方案**：  
避免函数调用，使用范围查询。

```sql
SELECT * FROM orders
WHERE create_time >= '2024-01-01 00:00:00'
  AND create_time <  '2024-01-02 00:00:00';
```

---

## 8. 排序优化

**问题场景**：  
大表排序查询慢。

**慢查询**：

```sql
SELECT * FROM orders ORDER BY create_time DESC LIMIT 10;
```

**优化方案**：  
为排序字段创建排序索引。

```sql
CREATE INDEX idx_orders_create_time_desc ON orders(create_time DESC);
```

---

## 9. 去重查询优化

**问题场景**：  
查询用户的不重复操作记录。

**慢查询**：

```sql
SELECT DISTINCT user_id
FROM user_actions
WHERE action_date >= '2024-01-01';
```

**优化方案**：  
使用 `GROUP BY` 替代 `DISTINCT`：

```sql
SELECT user_id
FROM user_actions
WHERE action_date >= '2024-01-01'
GROUP BY user_id;
```

---

## 10. 锁竞争优化

**问题场景**：  
高并发下更新库存容易导致死锁。

**慢操作（易死锁）**：

```sql
UPDATE products SET stock = stock - 1 WHERE id = 100;
UPDATE products SET stock = stock - 1 WHERE id = 50;
```

**优化方案**：  
在应用层确保更新操作始终按照固定的主键顺序进行，以避免循环等待。例如，总是先更新ID较小的记录，再更新ID较大的记录。

**示例代码（应用层逻辑）**：
```php
// 假设要更新的id为[100, 50]
$ids = [100, 50];
// 排序以保证更新顺序
sort($ids); // [50, 100]

$pdo->beginTransaction();
foreach ($ids as $id) {
    $stmt = $pdo->prepare("UPDATE products SET stock = stock - 1 WHERE id = ?");
    $stmt->execute([$id]);
}
$pdo->commit();
```

---

## 🧠 实际工作经验总结

在实际工作中进行 SQL 优化，我通常会遵循以下步骤：

1. **分析执行计划**  
   使用 `EXPLAIN` 命令了解 SQL 执行方式，找出瓶颈。

2. **监控慢查询日志**  
   定期排查慢查询，优先优化耗时语句。

3. **检查索引使用情况**  
   确保查询、排序、关联字段有合适索引。

4. **优化查询逻辑**  
   避免子查询、函数调用、全表扫描。

5. **分库分表**  
   针对超大表，考虑进行水平拆分，缓解单点压力。

---

通过这些优化手段，可以显著提升数据库查询性能，从而改善整体系统响应速度和用户体验。
