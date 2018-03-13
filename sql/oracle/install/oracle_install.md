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

### 4）创建oracle插入帐号

### 5) 创建Vault用户帐号

### 6) 取消设置oracle插入环境变量


### 4) 创建安装目录
```shell
mkdir -p /home/oracle/oracle
chown -R oracle:oinstall /home/install/oracle
chmod -R /home/oracle/oracle
```

## 六、配置网络

## 七、存储选项

## 八、配置文件系统存储

## 九、配置存储

## 十、插入和配置Oracle Grid Infrastructure

## 十一、插入Oracle Database

## 十二、插入后操作

## 十三、卸载Oracle Software

## A 手动完成预装

## B 使用Response files插入和配置oracle database

## C 优化

## D 复制数据库

## F 管理数据库端口号
