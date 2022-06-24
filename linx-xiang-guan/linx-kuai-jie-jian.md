# Linx快捷键

**Tab：**自动补全命令名字、文件路径、服务名、软件名

**Crtl+L：**清屏（相当于clear命令）

**Crtl+C：**放弃当前任务，中止

**Esc+.：**快速粘贴前一条命令的最后一个参数

**ls：**用来列出（list）目录下有哪些文件，列出对象的详细信息（大小、权限、修改时间……）\
&#x20;   \-l，长格式（long）列出对象的详细信息（大小、权限、修改时间……）\
&#x20;   \-h，显示更易懂（human readable）的容量单位\
&#x20;   \-d，只看目录（directory）本身的信息（而不是看目录下面有哪些内容）

**pwd：**用来列出当前在哪个目录下（print working directory）

**cd：**用来改变工作目录（change directory）\
&#x20;   使用～表示当前用户的主目录，～zhangsan表示zhangsan的主目录（home/zhangsan）

**su：**切换到另一个用户身份（substitute user）\
&#x20;    建议加上 -l 选项（简写为 -）来模拟登陆过程\
&#x20;    管理员切换到其他用户，不需要密码\
&#x20;    普通用户切换到其他用户，需要验证对方密码

**mkdir：**创建新的目录（make directory）

&#x20;    \-p，递归创建多层目录（parent），如果目录存在也不报错

**touch：**用来测试创建制定名称的文件（空文件）

**cat：**用来阅读短文件，直接显示整个文件的全部内容\
&#x20;    比如 cat /etc/hosts 看地址映射文件

**less：**用来阅读长文件，先显示文件的第一屏内容，通过PgUp、PgDn翻页阅读，q退出\
&#x20;    比如 less /proc/cpuinfo 查看当前主机的CP处理器信息

**cp：**用来复制文档\
&#x20;    常用选项 -r，（recursive 递归）复制目录的时候需要加\
&#x20;    比如 cp file1 file2 、 cp -r menu1 menu2

**rm：**用来删除文档\
&#x20;    常用选项 -r，删除目录的时候需要加； -f，强制（force）删除文档的时候需要加\
&#x20;    比如rm -rf /\* 删除根目录（谨慎操作）

**mv：**用来移动/改名文档

&#x20;    比如 mv file1 file2、 mv menu1 menu2



**命令的帮助man**

&#x20;    执行man 命令名  来获取这个命令的使用帮助

&#x20;    重点看SYNOPSIS（语法格式）、DESCRIPTION（选项含义和用法描述）\
&#x20;    按/word  查找包含word的文字描述，按n或者N来切换不同查找结果，按PgUp、PgDn翻页，按q退出



