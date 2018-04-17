#  README.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-02-25 14:55

# mysql的安装和配置文件
## 安装准备
###源码编译安装mysql
* [官方安装教程](https://dev.mysql.com/doc/refman/5.7/en/installing.html "office")

### 安装依赖包
### 下载相应的源码包
###新建MySql用户和用户组

\#Preconfiguration setup
```
$ groupadd mysql
$ useradd -r -g mysql -s /bin/false mysql
```

### 预编译
```
#/bin/bash
MYSQL_INSTALL_HOME=/opt/mybin/mysql/mysql5721
cmake .. -DCMAKE_INSTALL_PREFIX=${MYSQL_INSTALL_HOME} \
    -DINSTALL_MYSQLDATADIR="${bpath}/data" \
    -DMYSQL_DATADIR=${MYSQL_INSTALL_HOME}/data \
    -DTMPDIR=${MYSQL_INSTALL_HOME}/tmp \
    -DSYSTEMD_PID_DIR=${MYSQL_INSTALL_HOME}/tmp \
    -DMYSQL_UNIX_ADDR=${MYSQL_INSTALL_HOME}/tmp/mysql.sock  \
    -DSYSCONFDIR=${MYSQL_INSTALL_HOME}/etc \
    -DSYSCONFDIR=${MYSQL_INSTALL_HOME}/etc \
    -DWITH_INNOBASE_STORAGE_ENGINE=1 \
    -DWITH_PARTITION_STORAGE_ENGINE=1 \
    -DWITH_FEDERATED_STORAGE_ENGINE=1 \
    -DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
    -DWITH_MYISAM_STORAGE_ENGINE=1 \
    -DDEFAULT_CHARSET=utf8  \
    -DDEFAULT_COLLATION=utf8_general_ci \
    -DWITH_EXTRA_CHARSETS=all \
    -DENABLED_LOCAL_INFILE=1 \
    -DWITH_BOOST=/opt/mybin/mysql/tmp/mysql-5.7.21/boost/boost_1_59_0/ \
```
### 编译安装
    make -j 6
    sudo make install
### 创建一些文件夹
    mkdir mysql-files
    chown mysql:mysql mysql-files
    chmod 750 mysql-files
### Postinstallation Setup and Testing
#### 自动生成root密码
    bin/mysqld --initialize --user=mysql
         --basedir=/opt/mysql/mysql
         --datadir=/opt/mysql/mysql/data

#### 无root密码
    bin/mysqld --initialize-insecure --user=mysql
         --basedir=/opt/mysql/mysql
         --datadir=/opt/mysql/mysql/data
#### 指定my.ini文件
    ../bin/mysqld --defaults-file=/opt/databases/mysql/bin/etc/my.cnf  
        --initialize-insecure 
        --user=mysql
### 自动提供安全连接
    bin/mysql_ssl_rsa_setup
### 开始启动服务（必须）
    bin/mysqld_safe --user=mysql &
### 移动启动服务文件到相关目录
    cp support-files/mysql.server /etc/init.d/mysql.server
