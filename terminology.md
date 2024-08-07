# 主要概念


## 实例类型

PostgreSQL实例目前支持普通版和高可用版实例。

## 版本

PostgreSQL实例目前支持的版本如下表，用户可以根据需求选择相应的云数据库版本。

| 版本名   |
|---------|
| 9.6     |
| 10.4    |
| 12.8    |
| 13.4    |
| 14.10   |
| 16.2    |

## 计算规格

云数据库的CPU和内存大小。用户可以根据对云数据库的硬件需求进行选择。

## 硬盘

云数据库的硬盘大小。用户可以根据对云数据库的硬件需求进行选择。

## 付费方式

付费方式分为按年、按月、按时三种方式，且支付方式皆为预付费即提前支付相应服务周期的费用。

具体计费说明请参考“计费指南”文档。

## 数量

用户需要申请的云数据库数量，默认为1台，同时可以选择多台进行批量创建。

## 节点

PostgreSQL目前支持Master节点和Standby节点。

## 配置文件

配置文件包括云数据库运行的各类配置参数，用户可以根据需要进行自定义和修改，不同云数据库版本均提供相应默认配置文件。

配置文件包括默认配置文件和自定义配置文件，自定义配置文件由用户创建和导入。

## 管理员

默认提供超级管理员（root）权限，允许用户自定义管理员密码。

## 实例名称

用户可以自定义云数据库实例的名称。

## 资源ID

用户创建云数据库实例后，系统会自动生成资源ID，资源ID全局唯一。

## IP和端口

IP为用户访问云数据库的内网地址，创建云数据库成功后自动生成，目前不提供外网IP。

PostgreSQL默认端口为5432。

## 备份

备份保存了某时间点云数据库的所有数据。云数据库提供自动备份和手动备份两种方式，防止数据丢失，避免误操作带来的风险。

## 日志

日志是用于记录云数据库操作事件的记录文件。包括数据库日志。

## 云数据库 UDB 服务等级协议（SLA）

UCloud UDB服务等级协议规定了UCloud向客户提供UCloud
UDB云数据库服务的一般性服务等级指标和服务注意事项。协议详情请点击[云数据库 UDB 服务等级协议（SLA）](sla/udb_sla)查看。

