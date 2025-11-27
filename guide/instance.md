# 实例管理
实例管理提供对数据库实例的基础管理能力，包括查看实例运行状态、启停实例、重启实例、升级主库、数据库回档、重置密码等操作，帮助用户便捷管理实例的日常运行。

## 查看PostgreSQL实例列表

进入产品主页会自动显示当前创建的实例列表。

![image](/images/instance/list.png)

## 重启PostgreSQL实例

进入产品主页， 在列表中找到对应的实例， 点击“操作”栏下的扩展按钮， 点击“重启”

![image](/images/instance/restart.png)

## 关闭PostgreSQL实例

进入产品主页， 在列表中找到对应的实例， 点击“操作”栏下的扩展按钮， 点击“关闭”

![image](/images/instance/close.png)

![image](/images/instance/close-confirm.png)

![image](/images/instance/close-done.png)

## 启动PostgreSQL实例
如果要启动处于关闭状态的PostgreSQL实例，首先选择需要启动的PostgreSQL实例，在右侧操作项中，点击“启动”按钮，弹出的确认对话框选择确定，即可启动PostgreSQL实例。

![image](/images/instance/start.png)

确认信息无误后点击“确定”
![image](/images/instance/start-confim.png)

完成后点击“关闭”
![image](/images/instance/start-done.png)

如果需要同时启动多个PostgreSQL实例，选择相应的PostgreSQL实例，选择列表页面上方的“启动”，弹出的对话框选择“确定”，即可启动多个PostgreSQL实例。


## 删除PostgreSQL实例

进入产品主页， 在列表中找到对应的实例， 点击“操作”栏下的扩展按钮， 点击“删除”

![image](/images/instance/delete.png)


## 查看PostgreSQL实例详情

进入产品主页， 在列表中找到对应的实例， 点击“操作”栏下的“详情”

![image](/images/instance/detail.png)


## 普通版提升为高可用
**前置条件**

已成功创建实例，当前处于运行中状态，且数据库类型为“普通版”。

**操作步骤**

1. 访问 [UDB PostgreSQL 实例列表](https://console.ucloud.cn/postgresql/postgresql)，选择需要更改配置的目标主库实例，点击操作列中“升级高可用”按钮。

![](/images/1764040399194-204aedd0-a95b-49d0-8bbd-7df52e54ef05.png)

2. 在抽屉页确认升级后的数据库信息，并点击“立即购买”。

> 说明：普通版提升为高可用，将按新旧规格差额补费。
>

![](/images/1764040531296-4a0ec681-5d41-4acc-9b22-6ddb69ea7396.png)



## 数据库回档
关于如何创建实例，详情请参考 [恢复](/upgsql/guide/recovery) 对应章节。

## 重置密码
**前置条件**

已成功创建实例，当前处于运行中状态，且数据库类型为“普通版”或“高可用版”。

**操作步骤**

1. 访问 [UDB PostgreSQL 实例列表](https://console.ucloud.cn/postgresql/postgresql)，选择需要更改配置的目标实例，点击操作列中“重置密码”按钮。

![](/images/1764048973228-e526f458-5a8d-4b07-851f-b28f100c507c.png)

2. 弹窗中设置新密码，“确认”提交，重置完成。

![](/images/1764048994808-c349c726-b242-40d2-9817-45e971d6c4a9.png)
