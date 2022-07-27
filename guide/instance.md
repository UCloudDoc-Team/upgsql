# 操作指南

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


## 配置文件

用户可以在左侧导航栏选择配置文件，进入配置文件管理页。

配置文件是PostgreSQL实例启动所需的重要组成，涉及相关的参数配置。PostgreSQL实例都需要使用特定的配置文件，UCloud提供默认配置文件，默认配置文件不可以修改。

### 导入配置文件

除了提供的默认配置文件外，用户也可以导入自定义配置文件，以便让PostgreSQL实例使用该配置。

点击配置文件上方的“导入”按钮，在弹出的输入框中填写“配置名称”、“描述”、“DB类型”，并选择本地的配置文件，点击确定后，即可完成导入。

![image](/images/pgv4config.png)

![image](/images/pgv4config2.png)

### 创建配置文件

除导入配置文件外，也可以直接在配置文件管理页创建配置文件。

点击“创建”按钮，在弹出对话框中输入“配置名称”、“描述”、“DB类型”，并且可以选择从哪一个现有配置中进行复制，点击“确定”后即可完成配置文件的创建操作。

![image](/images/pgv4config1.png)

### 查看配置文件

在配置文件列表中选择指定的配置文件，点击“详情”，可以查看其当前的所有参数项。

![image](/images/pgv4config3.png)

“使用该配置的数据库”显示哪些PostgreSQL实例使用了该配置文件。

### 编辑配置文件

在配置文件列表中选择自定义配置文件，点击“编辑”，可以查看其当前的所有参数项并可以进行编辑。

![image](/images/pgv4config4.png)

自定义参数需参考范围和字符类型。默认配置文件不允许编辑修改。如果用户需要在默认配置文件的基础上进行修改，可以选择将默认配置文件另存为新配置文件，创建成功后即可在新配置文件上进行自定义修改。

### 更改配置文件

对于PostgreSQL实例，用户可以更改当前配置文件。

选中PostgreSQL实例，在操作项中选择“修改配置文件”，在弹出的对话框中，选择需要更改的配置文件即可。

![image](/images/pgv4config5.png)

修改配置文件需要在重启实例后生效。

### 删除配置文件

配置文件支持删除操作。在配置文件列表页选中需要删除的配置文件，选中删除即可完成删除操作。

默认配置文件不可删除。

