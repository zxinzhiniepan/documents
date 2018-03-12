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

### 2) 插入额外包
- gentoo</br>
    echo "dev-db/oracle-instantclient highlight sdk sqlplus jdbc odbc tools" > /etc/portage/package.use/oracle-instantclient
    emerge -avt oracle-instantclient

ps:如果无法下载相应的包则到http://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html下载相应的包放入/usr/portage/distfiles

