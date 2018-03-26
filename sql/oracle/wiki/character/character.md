#  字符问题
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-25 23:07

## 字符问题(http://www.cnblogs.com/freewater/archive/2011/08/27/2155494.html)
要解决Oracle的客户端乱码问题关键是要把服务器端使用的字符集跟客户端使用的字符集统一起来。Oracle客户端（Sqlplus）通过NLS_LANG环境变量来确定客户端使用的字符集。NLS\_LANG参数由以下部分组成:
NLS\_LANG=<Language>_<Territory>.<Clients Characterset>

NLS\_LANG各部分含义如下:
LANGUAGE指定:
Oracle消息使用的语言
日期中月份和日显示
TERRITORY指定
货币和数字格式
地区和计算星期及日期的习惯
CHARACTERSET:
控制客户端应用程序使用的字符集
通常设置或者等于客户端(如Windows)代码页 
或者对于unicode应用设置为UTF8

## 原因
oracle字符集问题一般可以分为三类： 数据库字符集, sqlplus的字符集(客户端字符集), 终端程序的字符集（非oracle的）

## 数据库字符集
数据库字符集是所有字符问题的核心，只有数据库本身的字符集正确了，客户端的字符集才可能正确。这里只的客户端包括sqlplus以及我们自己读数据库的应用程序。
在定位问题时，我们需要先看看数据库当前是什么字符集。
1. 查询数据库字符集
```
select * from nls_database_parameters
```
其中 nls\_language表示了显示方式, 就是sqlplus的程序的显示字体,有SIMPLIFIED CHINESE,American america
其中  nls\_characterset是字符集设定, 常用的一些字符集有UTF8,US7ASCII，WE8ISO8859P1，ZHS16CGB231280，ZHS16GBK, AL32UTF8

2. 修改数据库字符集

## 客户端字符集
如果服务器端的字符集是正确的，
而在客户端下，如sqlplus仍然不能正确显示中文，这一般是由于服务器端的字符集设定与客户端的字符集设定不同造成的。只要将两者修改一致就可以解决问题。
1）查询客户端字符集
```
select * from nls_instance_parameters
```

客户端字符集环境select * from nls_instance_parameters,其来源于v$parameter，
表示客户端的字符集的设置，可能是参数文件，环境变量或者是注册表
```
select userenv('language') from dual;
```

会话字符集环境 select * from nls_session_parameters，其来源于v$nls_parameters，表示会话自己的设置，可能是会话的环境变量或者是alter session完成，如果会话没有特殊的设置，将与nls_instance_parameters一致。 
客户端的字符集要求与服务器一致，才能正确显示数据库的非Ascii字符。如果多个设置存在的时候，alter session>环境变量>注册表>参数文件
字符集要求一致，但是语言设置却可以不同，语言设置建议用英文。如字符集是zhs16gbk，则nls_lang可以是American_America.zhs16gbk。  或者.zhs16gbk。注意zhs16gbk前面那个点是必须的哦！！

2） 修改客户端字符集
oracle 的sqlplus会去读取OS中环境变量下的nls_lang信息（关键在这里，通过以上命令查看
```
select * from nls_database_parameters 显示

NLS_LANGUAGE                   AMERICAN                               
NLS_TERRITORY                  AMERICA                                
NLS_CURRENCY                   $   

select * from nls_instance_parameters 显示

NLS_LANGUAGE                   SIMPLIFIED CHINESE                     
NLS_TERRITORY                  CHINA                                  
NLS_SORT                                                              
NLS_DATE_LANGUAGE                                                     
NLS_DATE_FORMAT                                                       
NLS_CURRENCY     

select * from nls_session_parameters 显示  

NLS_LANGUAGE                   SIMPLIFIED CHINESE                     
NLS_TERRITORY                  CHINA                                  
NLS_CURRENCY                   RMB  
```

 

也就是说会话字符集与终端字符集一致，而与数据库字符集设置不一致。

另一个数据库实例sc

数据库字符集
```
select * from nls_database_parameters 显示

NLS_LANGUAGE                   AMERICAN                               
NLS_TERRITORY                  AMERICA                                
NLS_CURRENCY                   $   

select * from nls_instance_parameters 显示

NLS_LANGUAGE                   AMERICAN                               
NLS_TERRITORY                  AMERICA                                
NLS_SORT                                                              
NLS_DATE_LANGUAGE                                                     
NLS_DATE_FORMAT                                                       
NLS_CURRENCY    
```

在linux下
```
select * from nls_session_parameters 显示  

NLS_LANGUAGE                   AMERICAN                               
NLS_TERRITORY                  AMERICA    
```

用TOD 在windows下显示

```
NLS_LANGUAGE                   SIMPLIFIED CHINESE                     
NLS_TERRITORY                  CHINA                                  
NLS_CURRENCY                   RMB  
```

这个我还搞不明白为什么windows与linux会话字符集为什么不一样。

更搞不明白的是为什么两个数据库实例用同一个系统用户查询的，也就是说环境变量是一样的，但查询结果终端字符集为什么会不一样？初步分析（当然不一定对，欢迎指正）是两个数据库实例的参数文件不一致引起的，因为alter session>环境变量>注册表>参数文件，也就是说因为环境变量没有设置，但sc与st数据库实例参数文件不同，但是参数文件在什么地方设还不清楚，有高手欢迎指点！


但基本上问题锁定是因为终端字符集与数据库字符集不一致引起，在用户的环境变量中增加

export NLS_LANG=AMERICAN_AMERICA.UTF8
export LANG=en_US.UTF-8

问题解决

再次查看客户端字符集

```
select * from nls_instance_parameters 还是显示

NLS_LANGUAGE                   SIMPLIFIED CHINESE                     
NLS_TERRITORY                  CHINA                                  
NLS_SORT                                                              
NLS_DATE_LANGUAGE                                                     
NLS_DATE_FORMAT                                                       
NLS_CURRENCY     
```

但

```
select * from nls_session_parameters 显示  

NLS_LANGUAGE        AMERICAN

NLS_TERRITORY         AMERICA

NLS_CURRENCY         $
```

看来只要本次会话字符集正确就没问题了，经过验证确实如此，用

alter session set NLS_LANGUAGE=‘AMERICAN’后本次会话也不会出现乱码的。
）
NSL_LANG包括三个部分（语言_地区.字符集）就是V$NLS_PARAMETERS表中的NLS_LANGUAGE,NLS_TERRITORY,NLS_CHARACTERSET
例如可以在cmd中键入
```
set nls_lang="Simplified chinese_china.utf8"
set nls_lang="american_america.us7ascii"
set nls_lang="american_america.zhs16gbk"
set nls_lang="Simplified chinese_china.zhs16gbk"
set nls_lang=".utf8"
set nls_lang=".zhs16gbk"
set nls_lang=".us7ascii"
```

unix下类似, 不过nls_lang要大写NLS_LANG, 在.profile或这.bash_profile(根据你用的shell)里更改NLS_LANG可以长久保持环境变量值.


## 终端字符集
如果数据库字符集和sqlplus的字符集一致, 还是不能正确显示了, 那很可能就是你的终端应用程序的字符集不支持了. 例如你用bash登陆sqlplus, 如果你的bash是个小字符集, 那么就不能正常显示了.linux修改bash的字符集, 可以先键入locale, 看有哪些环境变量, 再用export设置.


## 结论
增加添加系统字符的支持(Gentoo)

```
#  eselect locale list
Available targets for the LANG variable:
[1]   C
[2]   en_US
[3]   en_US.iso88591
[4]   en_US.utf8 *
[5]   POSIX
[6]   zh_CN.gb10830
[7]   zh_CN.gb2312
[8]   zh_CN.gbk
[9]   zh_CN.utf8
[ ]   (free form)
```

数据库字符设置为AL32UTF8

添加环境变量

```
export NLS_LANGUAGE=AMERICAN_AMERICA.UTF8
export NLS_LANG=AMERICAN_AMERICA.UTF8
```
