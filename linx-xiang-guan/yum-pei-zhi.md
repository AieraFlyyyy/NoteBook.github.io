---
description: Yum配置
---

# Yum配置

#### 客户端配置文件：/etc/yum.conf、 /etc/yum.repos.d/\*.repo

\[仓库1标识]

&#x20; name = 仓库描述

&#x20; baseurl = 仓库的地址

&#x20; enabled = 1|0

&#x20; gpgcheck = 1|0   // 是否要对源进行校验

&#x20; \#gpgkey = ....    // 校验key

\[仓库2标识]

&#x20; ......



安装  自动补全、网络工具、vim编辑器、DNS查询包

```
yum -y install bash-completion net-tools vim-enhanced bind-utils
```



yum命令的基本用法（新工具为dnf，用法相同）：

&#x20;  yum clean all                    // 清除缓存

&#x20;  yum repolist                     // 列出可用的仓库（源）信息

&#x20;  yum list \[软件名...]            // 列出软件的安装情况

&#x20;  yum info 软件名...             // 查询某软件的详细信息

&#x20;  yum -y install  软件名...    // 安装指定软件包， -y起自动确认作用

&#x20;  yum -y reinstall  软件名...   // 找回丢失的软件包，不影响依赖包

&#x20;  yum -y remove  软件名...    // 卸载指定软件包（同时卸载依赖此软件包的其他软件包）

&#x20;  yum -y update  软件名...

&#x20;  yum search 关键词              // 根据关键词搜索相关的软件

&#x20;  yum provides "文件路径"    // 查询哪个软件包能提供xx文件



**RHEL7/8的调优服务tuned：**

&#x20;   ****    提供了大量预设的调优方案，旨在于简化调优实施，充分利用系统资源与能效。管理员可以针对不同的业务选择不同的优化策略。

配置目录：/etc/tuned、 /usr/lib/tuned/优化方案/

管理工具：tuned-adm

服务名：tuned

tuned-adm基本用法：

&#x20;    tuned-adm  list                      // 列出可用的优化方案

&#x20;    tuned-adm  recommend       // 查看系统推荐的优化方案

&#x20;    tuned-adm  profile                // 切换优化方案

&#x20;    tuned-adm  active                 // 查看当前活动方案&#x20;

&#x20;    tuned-adm  off                      // 关闭调优方案
