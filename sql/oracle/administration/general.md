#  general.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-21 13:49

数据库(与mysql有区别):相当于仓库，保存数据文件的。可以时文件系统，专用的磁盘格式等等

全局书库名：区分标识

数据库实例：操作数据库的一系列进程，相当于一个看管仓库的

表空间：逻辑概念，一系列数据文件的整合，物理上比不存在。

数据文件：物理概念，用于存储数据库的数据文件(表中的记录，索引,存储过程，试图，数据字典等).PS: 数据库表与数据文件不存在一一对应的关系

控制文件：数据库文件和日志文件位置

日志文件：
模式或模式对象：数据库对象(表，索引等)的集合, oracle会为每一给用户创建一个模式
数据字典：oracle存放有关数据库信息的地方，用于描述数据。
>静态数据字典: 用户访问时不变的数据</br>
>动态数据字典：用来记录与数据库相关的性能统计动态信息
