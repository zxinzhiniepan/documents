管理篇
MySQL 5.7 Reference Manual > SQL Statement Syntax > Database Administration Statements 
    (https://dev.mysql.com/doc/refman/5.7/en/sql-syntax-server-administration.html)

1、登陆
   shell >> mysql -h host -u user -p password

2 查看配置文件状态
    shell >> mysqld --verbose --help
    mysql >> show variables; == shell >> mysqladmin variables       #查看当前正在运行的系统变量值
    mysql >> show status;   == shell >> mysqladmin extended-status  #查看一些统计指标

3 创建新用户
    create user 'user1'@'localhost' identified by password 'not-so-secret'  //本地用户
    create user 'user1'@'%' identified by password 'not-so-secret'  //远程用户

    创建用户
    CREATE USER ... IDENTIFIED BY ...   

    修改用户
    ALTER USER ... IDENTIFIED BY ...

    修改用户权限
    GRANT ... IDENTIFIED BY ...

    SET PASSWORD ...
    SLAVE START ... PASSWORD = ...
    CREATE SERVER ... OPTIONS(... PASSWORD ...)
    ALTER SERVER ... OPTIONS(... PASSWORD ...)

4 删除用户
    drop user 'username'@'%';

5 修改用户权限
    grant all privileges on zhangsanDb.* to zhangsan@'%' identified by 'zhangsan'; //远程登陆
    grant all privileges on zhangsanDb.* to zhangsan@'localhost' identified by 'zhangsan'; //本地
    grant all privileges on zhangsanDb.* to zhangsan@'host' identified by 'zhangsan'; //指定host
    flush  privileges; //使权限生效

    all privileges：所有权限。
    select：读取权限。
    delete：删除权限。
    update：更新权限。
    create：创建权限。
    drop：删除数据库、数据表权限。
    dbName.tableName表示授予权限的具体库或表，常用的有以下几种选项：
    
    .：授予该数据库服务器所有数据库的权限。
    dbName.*：授予dbName数据库所有表的权限。
    dbName.dbTable：授予数据库dbName中dbTable表的权限。

    废除权限
    grant all privileges on zhangsanDb.* to zhangsan@'%' identified by 'zhangsan'; //远程登陆

6 查询用户

    查看mysql中所有的用户
    mysql>SELECT DISTINCT CONCAT('User: ''',user,'''@''',host,''';') AS query FROM mysql.user;

    查看具体的用户权限
    mysql>show grants for 'user'@'%';
