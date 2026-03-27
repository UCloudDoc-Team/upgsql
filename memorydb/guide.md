
# 创建 MemoryDB 实例

1. 在控制台选择"云数据库 UDB PostgreSQL">"MemoryDB 管理"，点击"创建 MemoryDB"按钮创建实例。

![image](/images/memorydb/memorydb_entry.png)

2. 创建页面中选择地域、可用区。

![image](/images/memorydb/memorydb_region_zone.png)

3. 配置底层 PostgreSQL 数据库版本（推荐 PostgreSQL 16 及以上，内置 pgvector 支持）及实例规格。

![image](/images/memorydb/memorydb_pg_config.png)

4. 配置网络：选择 VPC 及子网，确保 MemoryDB 实例与您的 AI 应用处于同一网络环境。

![image](/images/memorydb/memorydb_network.png)

5. 实例创建完成后，页面跳转至 MemoryDB 管理列表，等待初始化完成后即可使用。

![image](/images/memorydb/memorydb_list.png)

6. 点击实例"详情"，可查看连接信息（Host、Port、数据库名、用户名）。

![image](/images/memorydb/memorydb_detail.png)


