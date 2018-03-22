#  sqlplus.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-22 09:28

## 永久修改sqlplus的设置
修改在$ORACLE_HOME/sqlplus/admin/glogin.sql(该文件会在sqlplus启动时执行)
```shell
-- 更改登录SQL的头
set termout off

define loginname=idle
column global_name new_value loginname
select  USER||'@'||substr(global_name,1,decode(dot,0,length(global_name),dot-1))  global_name from (select global_name,  instr(global_name,'.') dot from global_name);
set sqlprompt '&loginname> '
sest termount on
-- end
set linesize 200    -- 设置行宽
set pagesize 30     -- 设置每页显示多少行
set history on      -- 开启历史命令
set trimspool on
set trimout on
set colsep " | "      -- 分隔符
```
## sqlplus一些设置
```
SQL> set linesize 200    -- 设置行宽
SQL> set pagesize 30     -- 设置每页显示多少行
SQL> set history on      -- 开启历史命令
SQL> set timing on;          --设置显示“已用时间：XXXX”
SQL> set autotrace on-;    --设置允许对执行的sql进行分析
SQL> set trimout on; --去除标准输出每行的拖尾空格，缺省为off
SQL> set trimspool on; --去除重定向（spool）输出每行的拖尾空格，缺省为off
SQL> set echo on               --设置运行命令是是否显示语句
SQL> set echo off; --显示start启动的脚本中的每个sql命令，缺省为on
SQL> set feedback on;       --设置显示“已选择XX行”
SQL> set feedback off;      --回显本次sql命令处理的记录条数，缺省为on
SQL> set colsep' '; --输出分隔符
SQL> set heading off;    --输出域标题，缺省为on
SQL> set pagesize 0;     --输出每页行数，缺省为24,为了避免分页，可设定为0。
SQL> set linesize 80;     --输出一行字符个数，缺省为80
SQL> set numwidth 12;     --输出number类型域长度，缺省为10
SQL> set termout off;    --显示脚本中的命令的执行结果，缺省为on
SQL> set serveroutput on;  --设置允许显示输出类似dbms_output
SQL> set verify off                     --可以关闭和打开提示确认信息old 1和new 1的显示.
```o
