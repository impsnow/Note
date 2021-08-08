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

