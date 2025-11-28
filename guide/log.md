# 日志管理
## 日志分类
云数据库 UDB PostgreSQL 控制台展示了慢查询日志和错误日志。在使用日志前，您有必要了解以下信息。

**慢查询日志**

慢查询是指执行时间超过预设的阈值的 SQL 查询语句，慢查询日志则是记录这些慢查询的日志文件。

在云数据库 PostgreSQL 版，预设的 SQL 查询执行时间阈值是 1s，该阈值通过 log_min_duration_statement 参数进行设定。该参数是云数据库 PostgreSQL 版提供的可修改参数，您可根据自己的业务需要自行修改。关于修改参数的详细信息，请参见 [参数管理](/upgsql/guide/parameter-manage)。

云数据库 PostgreSQL 版还提供了可修改参数 log_duration 用于控制是否打印每个语句的执行时间，该参数的取值可为 on 或 off。取值为 on 时，用户的所有 SQL 都会被当作慢 SQL 记录在日志中；取值为 off 时，只有执行时间大于 log_min_duration_statement 的 SQL 才会被当作慢 SQL 记录在日志中。off 为默认值。

如您希望在日志中只记录执行时间大于 log_min_duration_statement 的 SQL，则需要确保 log_duration 的取值为 off，并按需对 log_min_duration_statement 参数进行设定。关于修改参数的详细信息，请参见 [参数管理](/upgsql/guide/parameter-manage)。

**错误日志**

错误日志（ErrorLog）用于记录 PostgreSQL 服务器在启动、关闭及运行过程中产生的错误、警告、提示信息以及重要事件。该日志能够帮助用户识别运行异常、定位故障原因，并用于问题诊断与排查。



## 查询日志
云数据库 UDB PostgreSQL 支持在控制台查看错误日志及慢查询日志，可实时查询或指定时间查询。

**操作步骤**

1. 访问控制台 [UDB PostgreSQL 实例列表](https://console.ucloud.cn/postgresql/postgresql) 进入目标实例详情页；
2. 选择“日志管理”标签页；
3. 选择查询条件，点击“开始查询”按钮。

![](/images/1764050653302-de82d694-8d25-4449-8c74-b790a081e35d.png)





## 下载日志
云数据库 UDB PostgreSQL 支持下载实例日志。对于高可用实例，可同时下载主、备节点的日志文件。

**操作步骤**

1. 访问控制台 [UDB PostgreSQL 实例列表](https://console.ucloud.cn/postgresql/postgresql) 进入目标实例详情页；
2. 选择“日志管理”标签页，侧边选择“日志包”选项；
3. 点击“打包日志”，弹出选择日志类型、日志时间，日志包名称，点击“确定”提交，日志打包完成后，即可下载对应日志包。

![](/images/1764051197246-572ff958-1f9e-4d31-9310-e357ff62c877.png)



