#  startOracle.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-21 14:28

## 一、开启数据库
### 1. windows下打开相应的服务
##### OracleServiceSID: 数据库服务
##### OracleOra11g_home1TNSListener: 监听服务
##### OracleDBConsoleSID: 数据库控制服务

## 2. linux下(以gentoo为例)
##### a. 开启监听服务
```
lsnrctl start
```

##### b. 开启数据库
```
$ sqlplus sys/password as sysdba
启动数据库
SQL> startup
```

## 二、配置数据库
#### 1. 配置监听器(LISTENER)
位于服务端，监听客户端的连接请求
#### 2. 配置网路服务名(TNSNAME)
一般位于客户端，用户连接远程的服务端

## 三、连接数据库
#### sql*plus 工具
用户登录
```shell
$ sqlplus user/password
```

无用户直接登录
```shell
$ sqlplus /nolog
SQL> connect user/password
```

远程用户登录
##### 1. 直接登录(service_name:时全局数据库名)
```shell
$ sqlplus 用户名/密码@ip地址[:端口]/service_name [as sysdba]
```

##### 2. 进行网络配置 oracle9i和以前的版本
2.1图形化操作:Net Configuration Assistant--> 本地Net服务名配置-->添加->服务名->协议(选tcp)->主机名称->端口->完成。

2.2文本化操作：编辑$ORACLE_HOME/NETWORK/ADMIN/tnsnames.ora文件
```config
test =
(DESCRIPTION =
    (ADDRESS_LIST =
        (ADDRESS = (PROTOCOL = TCP)(HOST = ip或主机名称)(PORT = 1521))
    )
    (CONNECT_DATA =
        (SERVICE_NAME = 数据库的服务名称)
    )
)
```
```shell
$ sqlplus 用户名/密码@配置的名字（上面配置的test）[as sysdba]
```
