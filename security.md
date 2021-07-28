# PAM
（Pluggable Authentication Modules）可动态加载验证模块
Linux-PAM（即linux可插入认证模块）是一套共享库,使本地系统管理员可以随意选择程序的认证方式。

/etc/pam.d/
pam模块文件内容看，可以将pam配置文件分为四列，

第一列代表模块类型
第二列代表控制标记
第三列代表模块路径 /lib64/security/pam_unix2.so
第四列代表模块参数

e.g.  session    required     pam_selinux.so      open env_params

### 模块类型
认证管理（auth）：表示鉴别类接口模块类型用于检查用户和密码，并分配权限；
账号管理（account）：表示账户类接口，主要负责账户合法性检查，确认帐号是否过期，是否有权限登录系统等；
会话管理（session）：会话类接口。实现从用户登录成功到退出的会话控制；
密码（password）管理：令类接口。控制用户更改密码的全过程。也就是有些资料所说的升级用户验证标记。

### 控制标记 control_flag
required
requisite
sufficient
optional
include

## 常用pam模块

pam_limits.so
pam_rootok.so  su不要密码认证
pam_cracklib.so 密码强度

https://www.cnblogs.com/kevingrace/p/8671964.html


# 工具
陌生IP：shodan zoom eye
域名：whois
文件： virustotal

### stunnel 建立加密隧道
