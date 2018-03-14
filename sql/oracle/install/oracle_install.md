#  oracle_install.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-11 13:48

## 一、下载安装包
官方下载地址：http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html

## 二、安装oracle数据库的准备
### 1) 硬件检查
- 内存大于1GB
- 需要显卡
- 需要联网

### 2) 操作系统检查
- 需要安装openssh
- linux x86-64内核支持

### 3) 系统配置检查
- 分配至少1GB的/tmp分区
- swap分区在8GB-16GB
- 需要相应的组和用户
- oracle主目录使用ASCII字符

## 三、检查配置和硬件
### 1) 检查硬件和内存配置
- 1. 使用下列命令探测物理内存
    grep MemTotal /proc/meminfo

- 2. 探测swap分区
    grep SwapTotal /proc/meminfo

- 3. 探测/tmp可用空间
    df -h /tmp

- 4. 查看free RAM
    free

- 5. 查看系统构架
    uname -m

- 6. 验证共享内存
    df -h /dev/shm

## 四、操作系统的配置
### 1) 依赖包
    rpm
    ksh
    xdpyinfo
    libXi
    libXtst
    libXau
    libXi
    libaio
    sysstat
    elfutils
    unixODBC

### 2) 插入额外包
#### gentoo</br>
    echo "dev-db/oracle-instantclient highlight sdk sqlplus jdbc odbc tools" > /etc/portage/package.use/oracle-instantclient
    emerge -avt oracle-instantclient

ps:如果无法下载相应的包则到http://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html下载相应的包放入/usr/portage/distfiles

### 3) 内核参数设置
##### 1. 编辑或者创建 /etc/sysctl.d/97-oracle-database-sysctl.conf 添加以下参数
```conf
fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.shmall = 2097152
kernel.shmmax = 4294967295
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
```

##### 2. 改变当前内核参数
```
# /sbin/sysctl --system
```

##### 3. 确认参数已经正确设置
```
# /sbin/sysctl -a

```

##### 4. 重启系统

## 五、 配置用户、组和环境oracle database
```shell
# oracle inventory group
groupadd -g 54321 oinstall

# Create an OSDBA for ASM group
groupadd -g 54327 asmdba

# Create an OSOPER for asm group
groupadd -g 54328 asmoper

#create OSDBA group
groupadd -g 54322 dba

# OSOPER group
groupadd -g 54323 oper

# Create OSBACKUPDBA group
groupadd -g 54324 backupdba

# Create the OSDGDBA
groupadd -g 54325 dgdba

# Create the OSKMDBA group
group -g 54326 kmdba

# Create the OSRACDBA
groupadd -g 54330 racdba

# create user
useradd -u 54321 -g oinstall -G dba,asmdba,backupdba,dgdba,kmdba,racdba oracle
```
### 1) 所需要的操作系统和组
- 创建Oracle Inventory组
```shell
# groupadd -g 54321 oinstall
```
- 验证
```shell
$ grep "oinstall" /etc/group
oinstall:x:54321:grid,oracle
```
使用ID
```shell
$ id oracle
uid=54321(oracle) gid=54321(oinstall) groups=54321(oinstall),54322(dba), 
54323(oper),54324(backupdba),54325(dgdba),54326(kmdba),54327(asmdba),54330(racdba)
```

```shell
$ id grid
uid=54331(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),
54327(asmdba),54328(asmoper),54329(asmadmin),54330(racdba)
```


### 2) 具有标准和工作角色分离的组和用户

### 3) 创建操作系统权限组
#### 为ASM创建OSDBA组
```shell
# groupadd -g 54327 asmdba
```

#### 为ASM创建OSOPER组
```shell
# groupadd -g 54328 asmoper
```
#### 创建OSDBA组
```shell
# groupadd -g 54322 dba
```

#### 创建OSOPER组
```shell
# groupadd -g 54323 oper
```

#### 创建OSBACKUPDBA组
```shell
# groupadd -g 54324 backupdba
```

#### 创建OSDGDBA组
```shell
# groupadd -g 54325 dgdba
```

#### 创建OSKMDBA组
```shell
# groupadd -g 54326 kmdba
```

#### 创建OSRACDBA组
```shell
# groupadd -g 54330 racdba
```

### 4）创建oracle插入帐号
#### 创建oracle用户
```shell
# useradd -u 54321 -g oinstall -G dba,asmdba,backupdba,dgdba,kmdba,racdba oracle
```
#### 修改oracle用户
```shell
# usermod -g oinstall -G dba,asmdba,backupdba,dgdba,kmdba,racdba,oper oracle
```

### 5) 创建Vault用户帐号

### 6) 取消设置oracle插入环境变量
Unset Oracle installation owner environment variables before you start the installation.</br>
The environment variables you have set for the Oracle installation owner account you use to run the installation can cause issues if they are set to values that conflict with the values needed for installation.</br>
If you have set ORA_CRS_HOME as an environment variable, following instructions from Oracle Support, then unset it before starting an installation or upgrade. You should never use ORA_CRS_HOME as an environment variable except under explicit direction from Oracle Support.</br>
If you have had an existing installation on your system, and you are using the same user account to install this installation, then unset the following environment variables: ORA_CRS_HOME, ORACLE_HOME, ORA_NLS10, TNS_ADMIN, and any other environment variable set for the Oracle installation user that is connected with Oracle software homes.</br>
Also, ensure that the $ORACLE_HOME/bin path is removed from your PATH environment variable.</br>

## 六、配置网络

## 七、存储选项

## 八、配置文件系统存储

## 九、配置存储

## 十、插入和配置Oracle Grid Infrastructure

## 十一、插入Oracle Database
### 1) 创建安装目录
```shell
mkdir -p /home/oracle/oracle
chown -R oracle:oinstall /home/install/oracle
chmod -R /home/oracle/oracle
```


## 十二、插入后操作

## 十三、卸载Oracle Software

## A 手动完成预装

## B 使用Response files插入和配置oracle database

## C 优化

## D 复制数据库

## F 管理数据库端口号
