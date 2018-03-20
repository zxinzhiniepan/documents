#  ssh_no_login.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-20 15:28


## SSh无法登录
ssh远程连接linux，密码正确，但是无法连接

原因：<b>连接的用户不再SHHD允许登录的列表中。在/etc/ssh/sshd_config中加入用户(root帐号下)</b>
```shell
# override default of no subsystems
Subsystem       sftp    /usr/libexec/openssh/sftp-server
AllowUsers root
AllowUsers admin
AllowUsers xxx
```

为了安全考虑，限制root远程登录
```shell
# /etc/ssh/sshd_config
PermitRootLogin no
AllowUsers theOne Beos
```

重启sshd服务
```shell
service sshd restart
# gentoo中使用
rc-service sshd restart
```
