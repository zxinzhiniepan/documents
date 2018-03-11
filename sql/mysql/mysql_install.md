#  README.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-02-25 14:55

# mysql的安装和配置文件
## 安装准备
###源码编译安装mysql
* 官方文档地址：https://dev.mysql.com/doc/refman/5.7/en/installing.html

### 安装依赖包
### 下载相应的源码包
###新建MySql用户和用户组
\#Preconfiguration setup
    groupadd mysql
    useradd -r -g mysql -s /bin/false mysql
### 预编译
    cmake .. -DCMAKE_INSTALL_PREFIX=/opt/databases/mysql/bin \
        -DMYSQL_DATADIR=/opt/databases/mysql/bin/data/mysql \
        -DWITH_BOOST=/home/zhanghua/.opt/databases/mysql/distfiles/tmp/mysql-5.7.21/boost/boost_1_59_0 \
        -DSYSCONFDIR=/opt/databases/mysql/bin/etc \
        -DWITH_INNOBASE_STORAGE_ENGINE=1 \
        -DWITH_PARTITION_STORAGE_ENGINE=1 \
        -DWITH_FEDERATED_STORAGE_ENGINE=1 \
        -DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
        -DWITH_MYISAM_STORAGE_ENGINE=1 \
        -DENABLED_LOCAL_INFILE=1 \         
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
