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
### 概念
LDAP是轻量目录访问协议(LightweightDirectory Access Protocol)

Active Directory 域 Services (AD DS)

Active Directory 存储有关网络上对象的信息，并让管理员和用户可以更容易地使用这些信息。通常包括共享资源，例如服务器、卷、打印机以及网络用户和计算机帐户

林、域树、子域都是活动目录(Active Directory)的逻辑单元。

林：林中包含了单个或多个域树和单个或多个子域。

dc-ou-cn

SRV记录：服务定位（SRV）资源记录，服务于AD

SRV记录是域控制器注册的，通过这些记录，客户机能够找到wen.com这个域控制器

### 配置
计算机加入域时输入域管理员账号密码（有加入该域权限的账号）

登录客户机可使用本地用户和域账号

GPO 组策略  一级一级查询，靠后生效，冲突时靠后一个生效。  链接OU

LSDOU顺序： （Local本地组策略—Site站点组策略—Domain域组策略—OU组织单位组策略）

下级OU设置了阻止继承；上级设置了强制（到此为止，不再向后查询）

