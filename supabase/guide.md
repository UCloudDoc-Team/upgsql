
# 创建Supabase实例

1.在控制台选择“云数据库 UDB PostgreSQL”>“Supabase管理”，点击“创建Supabase”按钮创建Supabase实例。

![image](/images/supabase/supabase_entry.png)

2.创建页面中选择地域、可用区。

![image](/images/supabase/supabase_region_zone.png)

3.配置Supabase密码和端口。Dashboard用户名固定为Supabase。

![image](/images/supabase/config_supabase_port_passwd.png)

4.配置PostgreSQL。下图中右侧“修改”按钮用于配置PostgreSQL的数据库版本及配置文件。图中下方“修改”按钮用于修改数据库端口。

![image](/images/supabase/supabase_pg_config_info.png)

![image](/images/supabase/config_pg_version.png)

5.网络配置。

![image](/images/supabase/network_config.png)

6.Supabase实例名称配置。

![image](/images/supabase/supabase_instance_name.png)

7.最后确认各项选择及金额，确认支付。

![image](/images/supabase/confirm_pay.png)

8.支付完成后，页面跳转回Supabase管理页，Supabase实例进行初始化，待初始化完成后即可使用。

![image](/images/supabase/supabase_mgm.png)

9.选择某一个Supabase实例点击“详情”会显示Supabase实例详细信息。

![image](/images/supabase/supabase_details.png)

# 配置外网访问

1.开启外网访问能力。开启外网访问时，Supabase服务会重启，重启后外网访问生效。

![image](/images/supabase/external_access_entry.png)

2.确认外网配置的带宽后，确认支付并等待Supabase重启后即可开启外网访问功能。

![image](/images/supabase/external_access_pay.png)

![image](/images/supabase/external_ui.png)

3.配置白名单。外网访问开启后，白名单默认是空的，此时还无法访问Supabase服务，需要在白名单中配置放行的IP或网段，Supabase服务才可以被访问到。

![image](/images/supabase/white_list.png)


# 配置S3存储

1.在Supabase实例详情页，点击“Storage配置”更改配置。提交配置更改之后，Supabase服务会重启，重启之后配置才会生效。

![image](/images/supabase/storage_config.png)

配置项说明：

- AWS_ACCESS_KEY_ID：S3存储公钥。对应US3令牌管理里面的公钥。

- AWS_SECRET_ACCESS_KEY：S3存储私钥。对应US3令牌管理里面的私钥。

- FILE_SIZE_LIMIT：上传文件的尺寸限制，单位为字节。

- GLOBAL_S3_BUCKET：存储所有文件的主存储桶名称。Supabase 会在这个桶里按照特定路径组织数据。

- GLOBAL_S3_ENDPOINT：S3存储访问域名，需要用支持AWS S3协议的域名。US3对应域名见[US3文档](https://docs.ucloud.cn/ufile/introduction/region)。

- REGION：S3存储痛所在地理区域。地域名称见[文档](https://docs.ucloud.cn/api/summary/regionlist?id=%e5%90%84%e5%9c%b0%e5%9f%9f-region-%e5%90%8d%e7%a7%b0%e5%88%97%e8%a1%a8)。

- S3_PROTOCOL_ACCESS_KEY_ID：在开启 S3 协议模式时使用的特定访问密钥 ID。

- S3_PROTOCOL_ACCESS_KEY_SECRET：对应 S3 协议模式的私钥。

- S3_PROTOCOL_ENABLED： 布尔值（true/false）。开启后，Supabase 将允许通过 S3 兼容模式进行通信，而不仅仅局限于标准的 AWS 环境。

- TENANT_ID：租户 ID。