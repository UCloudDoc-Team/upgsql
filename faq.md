# FAQs

## 如何访问PostgreSQL实例？

通过PostgreSQL Client访问，登录云主机，在命令行中输入： 

    psql -U$User -h$IP -p$port -d$postgres

$IP指定PostgreSQL实例的内网IP地址。

$Port指定PostgreSQL实例的端口。

$User指定PostgreSQL实例的管理员名称。

$postgres指定数据库

例如控制台上创建PostgreSQL实例，端口默认为5432、ip为10.19.11.111，管理员root：
```
psql -U root -h 10.19.11.111 -p 5432  -d postgres 
```
PostgreSQL实例仅支持通过云主机进行内网登陆。

## PostgreSQL的最大连接数是多少？

max\_connections是最大连接数，即允许客户端连接的最大连接数，增大连接可以允许接入更多的客户端，但设置过大同样会造成DB启动失败。目前PostgreSQL产品1G内存支持70个连接

