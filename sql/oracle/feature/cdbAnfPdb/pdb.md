#  pdb.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-22 10:46

## pdb的可拔插数据库名
1. 连接数据库
```
$ sqlplus / as sysdba
```
2. 查看数据库是否时cdb
```
SQL> select cdb from v$database;
```
3. 查看当前容器
```
SYS@ORCL> show con_name

CON_NAME
------------------------------
CDB$ROOT
```
4. 查看pdb信息(其中的name是pdb登录用户的服务名有关)
例：cdb登录的服务名为：orcl.xinzhiniepan.com
则pdb用户是：orclpdb.xinzhiniepan.com
```
SYS@ORCL> select con_id,dbid,guid,name,open_mode from v$pdbs;

CON_ID      DBID        GUID                                NAME                                OPEN_MODE
---------- ---------- -------------------------------- --------------------------------------------------------------------
2           2477889776  67E2B1FE633D5C5DE0530100007FDBCF    PDB$SEED                            READ ONLY
3           1517903734  67E302AFE7A6209CE0530100007F1AE7    ORCLPDB                             READ WRITE

Elapsed: 00:00:00.11
```

5. 创建和删除pdb
使用CREATE PLUGGABLE DATABASE可以从SEED来创建一个PDB。当前的容器必须是CDB root。
```
SYS@testdb> show con_name
 
 CON_NAME
 ------------------------------
 CDB$ROOT

 SYS@testdb> CREATE PLUGGABLE DATABASE test_pdb ADMIN USER testadm IDENTIFIED BY "rF" ROLES=(CONNECT) file_name_convert=('/data/oradata/testdb/pdbseed','/data/oradata/testdb/test_pdb') path_prefix='/data/oradata/testdb/test_pdb';

 Pluggable database created.

 SYS@testdb> show pdbs

 CON_ID CON_NAME                       OPEN MODE  RESTRICTED
 ---------- ------------------------------ ---------- ----------
 2      PDB$SEED                       READ ONLY  NO
 3      TEST_PDB                       MOUNTED
 ```
使用ROOP PLUGGABLE DATABASE来删除PDB
```
drop pluggable database test_pdb including datafiles;
```

6. 启动和关闭pdb
```
--启动
SYS@testdb> alter pluggable database test_pdb open;
 
Pluggable database altered.
 
SYS@testdb> show pdbs
 
    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 TEST_PDB                       READ WRITE NO
--关闭
SYS@testdb> alter pluggable database test_pdb close;
 
Pluggable database altered.
 
SYS@testdb> show pdbs
 
    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 TEST_PDB                       MOUNTED
```

7. 容器间的切换(orclpdb: 可拔插数据库的名)

```
//cdb-->pdb
SYS@ORCL> alter session set container=orclpdb;

Session altered.

Elapsed: 00:00:01.43

//pdb-->cdb
SYS@ORCL> alter session set container=orclpdb;

Session altered.

Elapsed: 00:00:01.43
```
8. 直接连接pdb
tnsname.ora(其中的SERVICE\_NAME是: 是数据库名换成可拔插数据库的name值)
```
# pdb用户配置
zhanghua=
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orclpdb.xinzhiniepan.com)
    )
  )
```
连接
```
$ sqlplus zhanghua/zhanghua@zhanghua

SQL*Plus: Release 12.2.0.1.0 Production on Thu Mar 22 11:40:48 2018

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Last Successful login time: Thu Mar 22 2018 11:20:44 +08:00

Connected to:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

ZHANGHUA@ORCLPDB> 
```

9. 创建表空间

```
SYS@testdb> alter session set container=test_pdb
 
Session altered.
 
SYS@testdb> create tablespace users datafile '/data/oradata/testdb/test_pdb/users01.dbf' size 200m;
 
Tablespace created.
 
SYS@testdb> select name from v$tablespace;
 
NAME
------------------------------
SYSTEM
SYSAUX
UNDOTBS1
TEMP
USERS
```

10. 用户管理
在cdb中只能创建全局用户(c##开头),会在cdb和所有的pdb中创建该用户(但是pdb中的全局用户需要另外授权才能够在pdb中访问)。在pdb中只能创建的用户为本地用户
```
SYS@testdb> show con_name
 
CON_NAME
------------------------------
CDB$ROOT
SYS@testdb> create user test identified by "abcd";
create user test identified by "abcd"
            *
ERROR at line 1:
ORA-65096: invalid common user or role name
 
 
SYS@testdb> create user c##test identified by "abcd";
 
User created.
 
SYS@testdb> alter session set container=test_pdb;
 
Session altered.
 
SYS@testdb> create user test identified by "abcd";
 
User created.
```
