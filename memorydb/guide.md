
# 创建 MemoryDB 实例

1. 在控制台选择"云数据库 UDB PostgreSQL">"MemoryDB 管理"，点击"创建 MemoryDB"按钮创建实例。

![image](/images/memorydb/memorydb_entry.png)

2. 创建页面中选择地域、可用区。

![image](/images/memorydb/memorydb_region_zone.png)

3. 配置底层 PostgreSQL 数据库版本（推荐 PostgreSQL 16 及以上，内置 pgvector 支持）及实例规格。

![image](/images/memorydb/memorydb_pg_config.png)

4. 配置网络：选择 VPC 及子网，确保 MemoryDB 实例与您的 AI 应用处于同一网络环境。

![image](/images/memorydb/memorydb_network.png)

5. 设置实例名称，确认配置与费用后，点击"立即购买"完成创建。

![image](/images/memorydb/memorydb_confirm.png)

6. 实例创建完成后，页面跳转至 MemoryDB 管理列表，等待初始化完成后即可使用。

![image](/images/memorydb/memorydb_list.png)

7. 点击实例"详情"，可查看连接信息（Host、Port、数据库名、用户名）。

![image](/images/memorydb/memorydb_detail.png)

# 配置外网访问

1. 在实例详情页，点击"开启外网访问"。开启过程中实例会短暂重启，重启完成后外网访问生效。

![image](/images/memorydb/external_access_entry.png)

2. 确认外网带宽配置后，点击确认支付，等待实例重启完成。

![image](/images/memorydb/external_access_pay.png)

3. 外网访问开启后，请在"白名单"中添加允许访问的客户端 IP 或网段，以保障数据安全。

![image](/images/memorydb/white_list.png)
