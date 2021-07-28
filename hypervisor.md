##
两种类型：
Bare-metal：IBM的PowerVM、VMware的ESX Sevrer、
	Citrix的XenServer、Microsoft的Hyper-V以及开源的KVM等
HOST-OS：VMware Workstation和VirtualBox

## VMWARE
vSphere 是一个产品套件
ESXi 是安装在物理机上面的服务。
vSphere Client 安装在笔记本或 PC 机上面，
用来访问 ESXi 服务并安装和管理上面的虚拟机
。vCenter Server 安装在了 ESXi 服务器的虚拟机里面。
vCenter 也可以安装在单独的物理服务器上面，
但是虚拟化不应该更好么?
vCenter 服务通常用在有很多 EXSi 服务和许多虚拟机的大规模环境中。
vCenter 也可以使用 vSphere client 来管理。
所以 vSphere client 可以在小环境中直接管理 ESXi 服务。
也可以在大规模的环境中，通过 vCenter 服务间接管理 ESXi 服务。
