
# 创建 Supabase 实例

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

2.外网访问支持选择计费方式，按需选择按带宽计费或按流量计费，并调整带宽上限，确认支付并等待Supabase重启后即可开启外网访问功能。（按流量计费为后付费）

![image](/images/supabase/external_access_config.png)

![image](/images/supabase/external_access_connection.png)

3.支持开启SSL加密连接。如需要关闭SSL，需要先关闭外网访问能力。

4.配置白名单。外网访问开启后，白名单默认是空的，此时还无法访问Supabase服务，需要在白名单中配置放行的IP或网段，Supabase服务才可以被访问到。

![image](/images/supabase/white_list.png)


# 配置 S3 存储

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

# 第三方登录

Supabase 支持统一用户认证体系，内置微信、支付宝、GitHub、Apple 等主流第三方登录，一键配置即可接入，免去多平台对接与联调成本，大幅减少开发工作量，加速产品上线。

## 使用条件

### 1. 开启外网访问

Supabase 实例已开启外网访问能力，才能使用第三方登录功能。

![image](/images/supabase/oauth_external_access.png)

### 2. 确保实例运行中

Supabase 实例状态为“运行中”。

### 3. 配置身份认证信息

已配置业务 URL、重定向 URL 和身份验证提供商凭据。本页面配置项的启用、禁用和编辑均需要重启 Supabase 实例后才能生效，预计重启 1 分钟，建议在业务低峰期操作。

### 4. 绑定 NAT 网关

已绑定 NAT 网关，并配置允许访问公网服务的出口规则。NAT 网关的地域、项目、VPC 和子网需要与 Supabase 实例保持一致。未绑定 NAT 网关时，Supabase 实例无法访问外部身份提供商，第三方登录、邮件登录和短信登录均无法正常使用。

#### 4.1 进入 NAT 网关页面

确认 NAT 网关的地域和项目与目标 Supabase 实例一致，进入[私有网络 VPC > NAT 网关](https://console.ucloud.cn/vpc/natgw)页面。

![image](/images/supabase/natgw_entry.png)

#### 4.2 创建 NAT 网关

单击“创建 NAT 网关”。“所属 VPC”和“指定子网”需选择 Supabase 实例所在的 VPC 和子网；“外网弹性 IP”可根据实际情况选择新购或已有 IP，其他参数按业务需求配置后完成购买。

![image](/images/supabase/natgw_create.png)

#### 4.3 配置出口规则

创建成功后，进入 NAT 网关详情页，在“出口规则”页签配置出口规则。可以使用默认出口规则，也可以根据业务需要添加自定义规则。NAT 网关详细说明请参见[NAT 网关文档](https://docs.ucloud.cn/vpc/guide/natgw)。

![image](/images/supabase/natgw_egress_rule.png)

?> 完成 NAT 网关配置后，建议在业务环境验证第三方登录、邮件登录或短信登录是否可以正常发送请求。

## 第三方登录配置说明

1. 确认 Supabase 实例已开通外网访问能力，且实例状态为“运行中”。

2. 配置业务 URL。

![image](/images/supabase/oauth_url_config.png)

3. 配置身份验证提供商凭据。根据业务所需的登录方式，按照页面提示获取 OAuth 凭据。以“支付宝登录”为例，开启 Alipay 登录按钮，将回调地址粘贴到支付宝开放平台开发设置中，再将 Alipay 侧提供的 APP ID、Private Key、Alipay Public Key 等信息填入。此处操作仅用于保存配置。

![image](/images/supabase/oauth_alipay_config.png)

4. URL 和 OAuth 凭据配置发生变更后，需要手动重启 Supabase 实例才能生效。

![image](/images/supabase/oauth_restart_confirm.png)

5. 确认配置成功启用后，建议在业务环境进行测试。终端用户在页面上单击支付宝登录并完成授权后，即可登录。

![image](/images/supabase/oauth_alipay_login.png)

## 邮件和短信登录

Supabase 支持通过邮件或短信进行用户认证。您可以在 Supabase 实例详情页的“第三方登录”页签中完成邮件服务和短信登录配置。

![image](/images/supabase/auth_email_sms_overview.png)

?> 邮件登录和短信登录均依赖公网访问。使用前，请先完成“使用条件”中的 NAT 网关配置。

### 邮件登录

配置邮件服务后，Supabase 可通过指定的 SMTP 服务器发送认证邮件。

#### 前提条件

- Supabase 实例状态为“运行中”。
- 已完成 NAT 网关配置，且实例可以访问 SMTP 服务器。
- 已从邮件服务供应商获取发件人邮箱地址、SMTP 服务器地址、端口、认证账号和认证密码或授权码。
- 邮件配置保存后需要重启 Supabase 实例才能生效，请在业务低峰期操作。

#### 配置步骤

1. 在“第三方登录”页面，单击“邮件服务”区域的“配置”。

![image](/images/supabase/email_login_config.png)

2. 打开服务开关，并根据邮件服务供应商提供的信息配置以下参数：

| 参数 | 是否必填 | 说明 |
| --- | --- | --- |
| 发件人邮箱地址 | 是 | 用于发送认证邮件的邮箱地址。 |
| 发件人显示名称 | 否 | 收件人看到的发件人名称。 |
| SMTP 服务器地址 | 是 | 邮件服务供应商提供的 SMTP 服务器地址。 |
| SMTP 端口 | 是 | 可选择“SSL 隐式加密（465）”或“TLS 显式加密（587）”。 |
| SMTP 认证账号 | 是 | 登录 SMTP 服务器所用的账号。 |
| SMTP 认证密码/授权码 | 是 | SMTP 账号对应的密码或授权码，请以邮件服务供应商的要求为准。 |

3. 勾选“我已了解，需手动重启实例后配置才生效”，单击“保存配置”。

4. 返回“第三方登录”页面，单击“重启并生效配置”，等待 Supabase 实例重启完成。

5. 在业务环境中触发一次邮件发送流程，验证邮件服务是否配置成功。

!> 保存邮件配置后，必须手动重启 Supabase 实例，配置才会生效。未重启前，邮件登录仍将使用原配置或不可用。

### 短信登录

短信登录目前仅支持使用 UCloud USMS 服务作为短信供应商。

#### 前提条件

- Supabase 实例状态为“运行中”。
- 已完成 NAT 网关配置，且实例可以访问 UCloud USMS 服务。
- 已开通[UCloud USMS 云通信服务](https://console.ucloud.cn/usms)，并创建可用的短信签名和短信模板。
- 已获取 USMS 项目 ID、Access Key ID 和 Key Secret，建议使用权限最小化的密钥并妥善保管。
- 短信配置保存后需要重启 Supabase 实例才能生效，请在业务低峰期操作。

#### 配置步骤

1. 在“第三方登录”页面，单击“短信登录”区域的“配置”。

![image](/images/supabase/sms_login_config.png)

2. 打开服务开关，短信供应商选择“UCloud”。

3. 配置短信服务基础信息：

| 参数 | 是否必填 | 说明 |
| --- | --- | --- |
| 项目 ID | 是 | 默认为当前 Supabase 实例所在项目 ID，如需修改请参考页面提示文档。 |
| Access Key ID | 是 | UCloud 账号或项目对应的公钥。 |
| Key Secret | 是 | 与 Access Key ID 对应的私钥。 |

4. 单击“立即前往”进入 USMS 控制台，完成短信签名和短信模板配置后，返回当前页面填写“短信签名 ID”和“短信模板 ID”。

5. 勾选“我已了解，需手动重启实例后配置才生效”，单击“保存配置”。

6. 返回“第三方登录”页面，单击“重启并生效配置”，等待 Supabase 实例重启完成。

7. 在业务环境中触发一次短信发送流程，验证短信服务是否配置成功。

!> 保存短信配置后，必须手动重启 Supabase 实例，配置才会生效。短信签名、短信模板以及账号权限需满足 USMS 服务的发送要求。
