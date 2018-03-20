#  oracle_after_install.m
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-19 08:45

## 配置防火墙(开放监听器的端口)
 iptables -I INPUT -p tcp  --dport  1521 -j ACCEPT

## 修改.bashrc(必须安装完数据库再添加, 各个值必须对应)
```
export PATH=$PATH:/usr/local/bin

# java
export JAVA_HOME=/opt/developments/java/bin/jdk/jdk1.8.0_162
# java9
#export JAVA_HOME=/opt/developments/java/bin/jdk/jdk-9.0.4
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export _JAVA_OPTIONS='-Dawt.useSystemAAFontSettings=lcd'

# Put your fun stuff here.
export ORACLE_BASE=/home/oracle/app/oracle
export ORACLE_HOME=/home/oracle/app/oracle/product/db
# 其中的ORACLE_SID的值必须对应数据库的sid
export ORACLE_SID=xdb
export ORACLE_INVENTORY=/home/oracle/app/oraInventory
export ORACLE_BASE ORACLE_SID ORACLE_HOME
export PATH=$ORACLE_HOME/bin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH
export TMP=/tmp
export EDITOR=vim
export VISUAL=vim
```


