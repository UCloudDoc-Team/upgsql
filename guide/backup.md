

# 备份管理

在实例“详情”页面，切换到“备份管理”标签页即可进行备份管理。

![image](/images/backup/main.png)

## 备份方式

备份分为自动备份和手工备份两种。

### 自动备份

自动备份默认每周天 0:00~1:00进行一次备份。

![image](/images/backup/default.png)

备份数据保留最近7份，最长保留30天，如果需要长期保存备份数据，请自行下载保存。

### 手工备份

PostGreSQL支持手工备份，用户可以保存某些关键时间点的重要数据备份。创建手动备份时，用户只需要输入备份名称，后台会立刻开始进行备份工作。

![image](/images/backup/manual.png)

手工备份允许保留最近3份，如果超过3份会自动删除最早的手动备份。备份数据最长保留30天，如果需要长期保存备份数据，请自行下载保存。

## 修改自动备份策略

在“备份管理”首页，点击“自动备份策略”按钮来打开自动备份策略修改窗口

![image](/images/backup/modified.png)

修改完成点击确定即可。

## 备份下载

用户可以下载自动备份和手工备份生成的备份文件。

打开备份管理首页，默认会显示当前可用的备份列表。

![imagel](/images/backup/list.png)

选择对应的备份，点击下载即可弹出下载框进行下载。

![image](/images/backup/download-default.png)

默认只显示内网下载链接， 如果需要外网下载链接，点击“其它地址”

![image](/images/backup/downloadotheraddress.png)

默认下载链接有效期为4小时， 如果需要更长的下载时间， 可自行调整下载链接有效期， 最长支持7天。

![image](/images/backup/download-self-defined.png)

