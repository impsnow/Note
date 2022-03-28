# 桌面环境

X 为一个协议，当前版本为11

Xorg是X协议的软件实现， 即X协议的服务端, 如 xrdp ，vnc

X client： 也是窗口管理器（WM），负责窗口移动，放大缩小。
如KDE，GNOME，XFCE，LXDE

# 终端
/dev/tty teletypes控制台终端

/dev/pty pseudo-terminal slave伪终端（xshell，putty）

# 性能分析
### 系统级性能分析工具 — Perf
基于事件采样

### top

wa：iowait

hi：irq ，系统处理硬件中断所消耗的时间，较高时可能外设出现问题， 可检查/proc/interrupts文件

st：steal，存在于虚拟机中，等待cpu调度的时间

### free
used：已经使用的内存大小(这里面包含cached和buffers和shared部分)。

-buffers/cache：正在使用的内存大小(注意不是used部分，因为buffers和cached并不是正在使用的，组织和人民需要是它们是可以释放的)，其值=used-buffers-cached。

+buffers/cache：可用的内存大小(同理也不是free表示的部分)，其值=free+buffers+cached。

buffers：写缓存

cache： 读缓存

### patching
Ubuntu/debian：
```
 apt update
 apt upgrade -y
```
centos：
```
yum makecache
yum update -y
```
suse:
```
zypper ref
zypper up -y
```

# shell
脚本推出
exit 1

set -e

trap + kill

# supervisor

```
environment=JMX_OPTIONS=""
command=/var/apps/batch/batch-task/bin/start.sh -mq
autostart=true
autorestart=true
stdout_logfile=/var/apps/logs/supervisor/batch-task-start.stdout.log
stderr_logfile=/var/apps/logs/supervisor/batch-task-start.stderr.log
stdout_logfile_maxbytes=100MB
stdout_logfile_backups=5
stdout_capture_maxbytes=1MB
stdout_events_enabled=false
stderr_logfile_maxbytes=100MB
stderr_logfile_backups=5
stderr_capture_maxbytes=1MB
stderr_events_enabled=false
loglevel=info
priority=1100
user=marketin
startsecs=5
```
# iptables
iptables不是防火墙，而是实现防火墙功能的工具。

四表五链：
- filter 表：控制数据包是否允许进出及转发，可以控制的链路有 INPUT、FORWARD 和 OUTPUT。
- nat 表：控制数据包中地址转换，端口映射，可以控制的链路有 PREROUTING、INPUT、OUTPUT 和 POSTROUTING。
- mangle：修改数据包中的原数据，可以控制的链路有 PREROUTING、INPUT、OUTPUT、FORWARD 和 POSTROUTING。
- raw：控制 nat 表中连接追踪机制的启用状况。对报文设置一个标志，决定数据包是否被状态跟踪机制处理。可以控制的链路有 PREROUTING、OUTPUT。

chain：

INPUT：      作用于进入本机的包

OUTPUT：     作用于本机送出的包      

FORWARD：    匹配穿过本机的数据包（转发）       

PREROUTING： 用于修改目的地址（DNAT）       

POSTROUTING：用于修改源地址 （SNAT）


```
iptables-save > /etc/sysconfig/iptables

iptables-store < file_name
```

# PAM （Pluggable Authentication Modules） 身份验证

Linux 在身份验证进程中使用 PAM（可插拔身份验证模块）作为用户和应用程序之间的中间层。PAM 模块在整个系统范围内可用，因此任何应用程序都可以请求 PAM 模块。

## 配置文件的名称
系统管理员维护的配置文件统一放置在 /etc/pam.d/ 目录中(高优先级)，软件包自带的默认配置文件统一放置在 /usr/lib/pam.d/ 目录中(低优先级)，对同时存在于两个目录中的同名文件来说，前者会覆盖后者。注意，文件名中的字母只能小写，并且拥有特殊的含义，文件名必须等于应用程序内部硬编码的"服务名"(同一个应用程序可以拥有多个不同的"服务名")。例如： su 总是读取 /etc/pam.d/su (若运行"su -l"则读取 /etc/pam.d/su-l )、 sudo 总是读取 /etc/pam.d/sudo (若运行"sudo -i"则读取 /etc/pam.d/sudo-i )、 sshd 总是读取 /etc/pam.d/sshd 。

名为"other"的特殊配置文件，表示当应用程序未能找到所需的配置文件(例如"su"或"sudo")时，应该使用的默认配置(一般是拒绝一切请求)。除"other"之外，还有其他一些配置文件也比较特别，这些配置文件并不对应某个"服务名"，而是专用于被其他配置文件引用，例如"password-auth"以及"system-auth"等等。

- 基于目录的配置 (/etc/pam.d/)： 依赖于 PAM 机制的每个服务（或程序）在 /etc/pam.d/ 目录中都有各自的配置文件。例如，可以在 /etc/pam.d/sshd 文件中找到 sshd 的服务。
- 通过/lib/security/ 或 /lib64/security/ 中的各个 PAM 模块来实现，模块文件名一般都符合 pam_*.so 格式。
- 为防止配置文件缺失可能导致的隐蔽故障与安全漏洞，应该确保存在一个严苛的默认配置(/etc/pam.d/other)。可以额外加入 pam_warn.so 模块，以确保系统管理员可以从系统日志(syslog)中获得警告信息。
- 因为配置文件中的任何语法错误都会导致认证过程失败，所以务必确保语法正确。
- 去除符号链接会有效禁用 pam-config

TYPE  CONTROL MODULE_PATH  MODULE_ARGS

TYPE: 若在类型前加上"-"(减号)前缀，则表示即使模块不存在，也不会影响认证结果，更不会将此事件记录到日志中，对于那些可有可无的模块来说，这一特性非常有用。
- auth： 验证账户的凭据。注意，这里的"凭据"不仅仅指密码，而是泛指一切认证方式，例如：一次性密码、指纹、短信、IP地址、二维码。除验证凭据之外，还可以进一步执行更多的关联操作，例如：修改账户所属的组、显示特定的提示信息、赋予账户某些权限。
- account： 验证账户自身的权限。例如：账户是否已禁用、凭据是否已过期、是否只能在特定的时间段有效、是否已经达到最大登录数量、是否属于特定的组、是否允许控制台登录、是否允许远程登录。注意，账户自身的权限与是否通过凭据验证无关，例如，禁止从远程登录的账户，即使成功通过了凭据验证，也仍然无法从远程登录。
- password： 管理账户的凭据。例如：更新账户密码并确保新密码符合复杂性要求、以 SHA512 方式存储密码。注意，这里的"凭据"不仅仅指密码，而是泛指一切认证方式，例如：指纹、声纹、虹膜、二维码。此类型通常都与 auth 类型存在很强的耦合性。
- session: 在用户获得服务之前/后需要执行的各种操作。例如：创建家目录、设置与撤销环境变量、显示今日消息(motd)、设置 umask 、挂载目录。


CONTROL：指执行步骤和规则
- required: 栈成功的必要条件。即使失败，也要继续执行此栈中所有同类型的后继模块(也就是"类型"字段值相同的后继行)，但此栈最终必定返回失败结果。
- requisite: 栈成功的必要条件。一旦失败，将立即终止此栈，并立即返回失败结果。栈的返回值取决于第一个失败的"required"或"requisite"模块。但使用此关键字使得攻击者探测帐户名称成为可能。
- optional: 栈成功的可选条件
- include: 从"模块路径"(module-path)字段指定的文件中提取所有同类型的行(也就是"类型"字段值相同的行)，直接插入此处(等价于直接写在此处)。注意，此关键字并不创建子栈。
- substack: 从"模块路径"(module-path)字段指定的文件中提取所有同类型的行(也就是"类型"字段值相同的行)，组成一个子栈，再将该子栈的运行结果作为该行的结果插入此处(类似于程序设计中的函数调用)。例如，子栈中的"requisite"失败只会导致子栈本身终止并返回失败结果，而不会导致父栈立即终止。

常用模块：
- pam_access.so：auth,account,password,session 。基于来源(主机/网络/终端/$DISPLAY/服务名)的访问控制./etc/security/access.conf
- pam_debug.so：auth,account,password,session.调试PAM栈
- pam_nologin.so:auth,account.pam_nologin is a PAM module that prevents users from logging into the system when /var/run/nologin or /etc/nologin exists. The contents of the file are displayed to the user. The pam_nologin module has no effect on the root user's ability to log in.
- pam_motd.so:session.显示"今日消息"(motd)

http://www.jinbuguo.com/linux/pam.html








