#  oracle_after_install.m
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-19 08:45

## 配置防火墙(开放监听器的端口)
 iptables -I INPUT -p tcp  --dport  1521 -j ACCEPT

