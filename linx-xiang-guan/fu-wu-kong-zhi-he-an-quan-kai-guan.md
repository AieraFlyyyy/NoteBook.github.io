# 服务控制和安全开关

**systemctl服务控制：**

&#x20;    systemctl，系统控制器，用来管理Linux操作系统的开机/关机/服务资源运行状态\
&#x20;    直接执行 systemctl 列出可以管理的系统资源，包括各种系统服务\
&#x20;    控制服务当前运行状态： systemctl  start|stop|restart|status  服务名...\
&#x20;    控制服务开机自启： systemctl  enable|disable  服务名...  \[--now(立即执行)]



**firewalld、SELinux安全开关：**

&#x20;    firewalld防火墙的作用，内核的一套网络保护机制，通过firewalld服务来控制 \
&#x20;     ****      firewalld的状态控制：systemctl  enable|disable firewalld --now



**SELinux，Security-Enhanced Linux：**

&#x20;   美国NSA国家安全局提供的一套基于内核的增强的强制安全机制，主要针对用户、进程、文档做了一些安全标签及相关限制

&#x20;    SELinux通过内核启动参数或启动配置来控制\
&#x20;    SELinux的状态控制（Enforcing强制保护、Permissive宽松模式、Disabled禁用）：\
&#x20;     \#vim  /etc/selinux/config   SELINUX=Permissive/Disabled/Enforcing  （需要重启生效）\
&#x20;       setenforce  0|1        （立即生效成宽松|强制模式）\
&#x20;       getenforce               （查看配置结果）

****

**管理SELinux安全上下文、端口开放策略：**

&#x20;   semanage fcontext（安全上下文）/port（端口）&#x20;

&#x20;        \-a 添加  \
&#x20;        \-l 列出所有  \
&#x20;        \-d 删除 \
&#x20;        \-p + 协议 \
&#x20;        \-t + 策略类型&#x20;

比如： semanage port -a -t http\_port\_t -p tcp 82  \
&#x20;          // 添加一个基于tcp协议的82端口



**SELinux预设策略的开关控制：**

&#x20;     getsebool -a                         // 列出所有开关参数

&#x20;     setsebool -P + 开关参数       // 开=on、关=off ****&#x20;

&#x20;&#x20;

**SELinux排错：**

&#x20;   yum -y install setroubleshoot-server

&#x20;   systemctl restart httpd                       // 试错

&#x20;   grep setrouble /var/log/messages     // 会提示如何查看详细信息\
&#x20;   查错sealert -l \
&#x20;   或者 jurnalctl -xe









&#x20;  &#x20;



