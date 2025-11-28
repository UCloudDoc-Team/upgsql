# 参数管理
本文介绍云数据库 PostgreSQL 版在实例中提供的可修改参数的相关信息，如何查看、修改数据库实例参数来满足提升性能或适应特定需求。

## 查看参数
1. 访问[UDB PostgreSQL 实例列表](https://console.ucloud.cn/postgresql/postgresql)，找到目标实例，点击“操作”列中的“详情”按钮。

![](/images/1763976397536-3d622935-08a5-46be-8f5c-1e1466af58cd.png)

![](/images/1763980181061-977aab53-f8af-489f-a45a-d3e312596dee.png)

## 修改参数
1. 访问 [UDB PostgreSQL 实例列表](https://console.ucloud.cn/postgresql/postgresql)，找到目标实例，点击“操作”列中的“详情”按钮。
2. 点击“参数管理”页签，然后点击页面左上角的“编辑”按钮。

![](/images/1763980214102-7b85ad37-e8b0-4edc-a226-9ebbd041a8d9.png)

3. 修改“参数值”列，可修改的参数及参数信息可参考 [参数说明](#参数说明)。

![](/images/1763976498807-e464fb1f-8be5-498d-9242-0cbf67c3fc7f.png)

4. 核对修改项，点击“确认”提交。

![](/images/1763979019038-b6c779c5-a307-43d9-8131-d9fd9d002c13.png)

> 注意：
>
> + 为保证实例的稳定，控制台仅开放部分参数的修改，如果找不到需要修改的参数，请[提交工单](https://spt.ucloud.cn/)处理。
> + 修改参数值时请参见控制台上 **PostgreSQL 参数**页签中的**参数管理**列。
> + 部分参数修改后在提交时会立即重启实例，具体请参见控制台上**参数管理**页面中的是否重启列。重启实例会造成连接中断，重启前请做好业务安排，谨慎操作。
> + 支持同时对多个参数的运行值进行批量修改。
>





## 应用参数模板
1. 访问 [UDB PostgreSQL 实例列表](https://console.ucloud.cn/postgresql/postgresql)，找到目标实例，点击“操作”列中的“详情”按钮。
2. 点击“参数管理”tab页，然后点击页面左上角的“应用参数模板”按钮。

![](/images/1763978789392-7976488c-9659-4809-b544-f511af273d55.png)

3. 选择模板类型及相应模板，并“确定”。

![](/images/1763977948929-0ff855d7-2ccc-4ed4-91aa-557c742a7eda.png)



## 默认参数模板管理
### 查看
1. 访问 [UDB PostgreSQL 参数模板管理 ](https://console.ucloud.cn/postgresql/param)，选择“默认参数模板”，可查看默认预置的参数模板。

![](/images/1763979275491-debf0078-9b2f-4a9e-a868-b327458e5c82.png)

2. 点击操作列“详情”按钮，跳转参数列表页查看参数详情。

![](/images/1763979400623-7374ce64-487d-434d-b22e-47ade3bdd248.png)

> **说明**
>
> 1. 如果仅需要修改单个实例的某个参数，请参见[修改参数](#修改参数)。
> 2. 预置的默认模板不支持编辑，如需创建模板可以移步“[用户参数模板](https://console.ucloud.cn/postgresql/param?type=custom)”操作。
>

### 应用到实例
1. 访问 [UDB PostgreSQL 参数模板管理 ](https://console.ucloud.cn/postgresql/param)，选择“默认参数模板”，点击操作列“应用到实例”按钮。

![](/images/1763979692586-87c600e8-43d4-4d93-93d9-b261e3124c08.png)

2. 打开抽屉页面，可选择待应用的实例，并“确认”提交。

![](/images/1763979660243-df729c0b-deb7-4b70-b143-064b37071987.png)

### 下载
支持在 [UDB PostgreSQL 参数模板管理 ](https://console.ucloud.cn/postgresql/param?type=default)下载默认参数模板。

![](/images/1763979945134-68dad34b-da81-47f3-a11e-6fed5fc257a1.png)



## 用户参数模板管理
### 查看
1. 访问 [UDB PostgreSQL 参数模板管理 ](https://console.ucloud.cn/postgresql/param?type=custom)，选择“用户参数模板”，可查看自定义的参数模板列表。

![](/images/1763980338488-fdc31794-4038-48aa-a4c5-c769711f2802.png)

2. 点击“详情”，即可跳转“参数列表”页，查看支持的参数及参数信息。

![](/images/1763980434374-b7bbd5f5-2595-49ea-b602-28fff4bc4faf.png)

### 创建
1. 进入 UDB PostgreSQL 参数模板管理的[用户参数模板列表](https://console.ucloud.cn/postgresql/param?type=custom)，点击操作列的“创建”按钮。

![](/images/1763980616971-7107f8f3-ed5d-4e26-ad36-cddaf2ee618c.png)

2. 打开创建抽屉页，输入“参数模板名称”，“确认”提交。

![](/images/1763980075894-81f03987-e1ea-41d5-8e4b-26bd8ff1081c.png)

3. 提交成功后，在UDB PostgreSQL 参数模板管理的[用户参数模板列表](https://console.ucloud.cn/postgresql/param?type=custom) 选中刚刚添加的模板，点击“详情”进入“参数列表”页。

![](/images/1763980804204-1fa182ef-1ac9-400a-a856-48a62b001746.png)

![](/images/1763980086894-c21a375d-5df4-45d7-82aa-21c01adc51a9.png)

4. “编辑”参数并“确认”。

![](/images/1763980081201-4ce64482-fe8f-4b2f-9df4-b2c861509170.png)

![](/images/1763980881487-ae6e7177-81f8-4369-995b-0c56a58bdad3.png)

### 应用到实例
**前置条件**

1. 已创建用户参数模板，详情参考 [用户参数模板管理](#用户参数模板管理) 中的「创建」内容；
2. 已创建实例且实例处于运行中状态。

**操作步骤**

1. 访问 UDB PostgreSQL 参数模板管理的[用户参数模板列表](https://console.ucloud.cn/postgresql/param?type=custom) ，点击操作列“应用到实例”按钮。

![](/images/1763981036802-387dbb14-b462-4d5b-9d9f-501b08c34708.png)

2. 选择待应用的实例，“确认提交”。

![](/images/1763981031618-0defd579-3d80-48ba-9b64-789a121d424f.png)

3. 弹窗会实时返回应用状态，确认成功后，即可“关闭”弹窗。

![](/images/1763981089887-00d9d990-46f0-47e9-99a5-96d50ee634ab.png)



### 删除
1. 访问 UDB PostgreSQL 参数模板管理的[用户参数模板列表](https://console.ucloud.cn/postgresql/param?type=custom) ，点击操作列“删除”按钮并“确认”。
2. 确认删除成功后，即可关闭“弹窗”。

![](/images/1763981234744-5b433612-98d9-403a-bf0f-5f1ec63fb6a2.png)

> 说明：
>
> 1. 允许删除已经应用到实例的用户参数模板，删除后原实例将使用默认模板参数。
>

### 克隆
1. 访问 UDB PostgreSQL 参数模板管理的[用户参数模板列表](https://console.ucloud.cn/postgresql/param?type=custom) ，点击操作列“克隆”按钮。
2. 打开抽屉页，输出“参数模板名称”并“确定”，即可完全复制模板及参数配置。

![](/images/1763981724669-86fb8c03-4b2c-47f1-9d53-187cc8a83dd8.png)



### 下载
支持在 UDB PostgreSQL 参数模板管理的[用户参数模板列表](https://console.ucloud.cn/postgresql/param?type=custom) 下载默认参数模板。



## 参数说明
| <font style="color:rgb(82, 96, 117);">参数名称 | <font style="color:rgb(82, 96, 117);">字符类型 | <font style="color:rgb(82, 96, 117);">默认参值 | <font style="color:rgb(82, 96, 117);">参数值范围 | <font style="color:rgb(82, 96, 117);">是否重启 |
| --- | --- | --- | --- | --- |
| archive_mode | string | off | off,on,always | 否 |
| autovacuum_analyze_scale_factor | float | 0.1 | 0-1 | 否 |
| autovacuum_max_workers | int | 5 | 0-8388607 | 否 |
| autovacuum_vacuum_cost_delay | int | 0 | 0-100 | 否 |
| autovacuum_vacuum_scale_factor | float | 0.1 | 0-1 | 否 |
| bgwriter_delay | int | 20 | 10-10000 | 否 |
| checkpoint_completion_target | float | 0.9 | 0-1 | 否 |
| datestyle | string | 'iso,ymd' | AnyString | 是 |
| default_statistics_target | int | 100 | 1-10000 | 否 |
| default_text_search_config | string | 'pg_catalog.simple' | AnyString | 否 |
| effective_cache_size | int | 98304 | 1-2147483647 | 否 |
| hot_standby | string | | off,on | 是 |
| hot_standby_feedback | string | on | off,on | 否 |
| lc_messages | string | 'C' | AnyString | 否 |
| lc_monetary | string | 'zh_CN.UTF-8' | AnyString | 否 |
| lc_numeric | string | 'zh_CN.UTF-8' | AnyString | 否 |
| lc_time | string | 'zh_CN.UTF-8' | AnyString | 否 |
| logging_collector | string | on | off,on | 是 |
| log_checkpoints | string | on | off,on | 否 |
| log_connections | string | on | off,on | 否 |
| log_directory | string | 'pg_log' | AnyString | 否 |
| log_disconnections | string | on | off,on | 否 |
| log_duration | string | off | off,on | 否 |
| log_filename | string | 'postgresql-%Y-%m-%d_%H%M%S.log' | AnyString | 否 |
| log_line_prefix | string | | AnyString | 是 |
| log_lock_waits | string | on | off,on | 否 |
| log_min_duration_statement | int | 1000 | 1-2147483647 | 否 |
| log_rotation_age | int | 0 | 0-35791394 | 否 |
| log_rotation_size | int | 10240 | 0-2097151 | 否 |
| log_statement | string | ddl | none,ddl,mod,all | 否 |
| log_temp_files | int | 10000000 | 1-2147483647 | 否 |
| log_timezone | string | 'PRC' | AnyString | 否 |
| log_truncate_on_rotation | string | on | off,on | 否 |
| maintenance_work_mem | int | 16384 | 1024-2147483647 | 否 |
| max_replication_slots | int | 10 | 0-8388607 | 是 |
| pg_stat_statements.max | int | 10000 | 0-2147483647 | 是 |
| pg_stat_statements.track | string | all | top,all | 是 |
| shared_buffers | int | 32768 | 16-1073741823 | 是 |
| shared_preload_libraries | string | 'pg_stat_statements,pg_buffercache,pgml' | AnyString | 是 |
| superuser_reserved_connections | int | 10 | 0-8388607 | 是 |








