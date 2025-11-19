
https://www.rockylinux.cn/notes/freeipa-ldap-account-management-theory.html
LDAP（Lightweight Directory Access Protocol，轻型目录访问协议） 

通过 DIT（目录信息树，Directory Information Tree）组织所有数据:DIT 是LDAP 中存储数据的分层树状结构。根节点下通常以 dc（域名组件） 和/或o（组织名称） 开始，向下分层包含组织单元（ou）、通用名称（cn） 等条目。

        1. You must make sure these network ports are open:
                TCP Ports:
                  * 80, 443: HTTP/HTTPS
                  * 389, 636: LDAP/LDAPS
                  * 88, 464: kerberos
                  * 53: bind
                UDP Ports:
                  * 88, 464: kerberos
                  * 53: bind
                  * 123: ntp