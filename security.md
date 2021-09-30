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



# PKI (Public-Key Infrastructure)

X.509是定义公钥证书格式的标准

一个X.509证书包含一个公钥和一个标识(主机名、组织或个人)，由证书颁发机构签名或自签名。
### pem格式：Privacy Enhanced Mail
```
----BEGIN(label)-----
...
-----END(label)-----
```

可以存储 ".pem",".cer",".crt"(证书)或者".key"(公钥或私钥)

### PKCS     (Public Key Cryptography Standards)


### CSR    (certificate signing request )
认证请求由三个主要部分组成:认证请求信息、签名算法标识符和认证请求信息上的数字签名。第一部分包含重要信息，包括公钥。

有CSR必定有KEY，是成对的，CSR最终变成为证书crt，和私钥key配对使用。

证书下发后，CSR就没有用了，只是在交时候需要。 

### SSL Certificate
即X.509 Certificate

有几种不同的格式，例如 PEM，DER，PKCS#7 和 PKCS#12

### PFX
windows常用









