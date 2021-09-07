# 内存
taskmanager 中显示的内存为 private pyhsical memorary

resource monitor（remon.exe）：

commit: working set + pagefile

working set: shareable + private

shareable: 共享的物理内存

private: 专用的物理内存


https://blog.csdn.net/typing_yes_no/article/details/48496923?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.control&dist_request_id=f8c236b5-5d29-4c93-b505-9fc23629347f&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.control

# RDMA
RDMA （remote direct memory access）RDMA是一种概念，在两个或者多个计算机进行通讯的时候使用DMA， 从一个主机的内存直接访问另一个主机的内存
RDMA是一种host-offload, host-bypass技术，允许应用程序(包括存储)在它们的内存空间之间直接做数据传输。具有RDMA引擎的以太网卡(RNIC)--而不是host--负责管理源和目标之间的可靠连接。
使用时需网卡或交换机或软件支持  协议：InfiniBand ，RDMA过融合以太网(RoCE)，互联网广域RDMA协议(iWARP)


# AD

LDAP是轻量目录访问协议(LightweightDirectory Access Protocol)的缩写,一种树形结构数据库

