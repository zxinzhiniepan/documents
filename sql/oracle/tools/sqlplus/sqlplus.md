#  sqlplus.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-22 09:28

## 永久修改sqlplus的设置
修改在$ORACLE_HOME/sqlplus/admin/glogin.sql(该文件会在sqlplus启动时执行)
```shell
set linesize 200    -- 设置行宽
set pagesize 30     -- 设置每页显示多少行
set history on      -- 开启历史命令
```
