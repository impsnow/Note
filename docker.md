#
chroot
lxc
namespace
cgroup
overlay

多个容器可共用一个基础镜像（lowerdir），运行时增加可写层（upperdir），
容器中看到是merged（mount方式）
