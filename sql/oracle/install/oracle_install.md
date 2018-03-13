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

### 3) 配置用户、组和环境oracle database
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

### 4) 创建安装目录
mkdir -p /home/oracle/install/oracle
chown -R oracle:oinstall /home/oracle/install/oracle
chmod -R /home/oracle/install/oracle
