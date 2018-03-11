#  mybatis.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-03 08:58
 - http://www.mybatis.org/mybatis-3/zh/index.html

## 使用Mybatis
1. 导入相关依赖（jar包）
2. 配置mybatis核心配置文件(configuration.xml)(子标签顺序不能乱)
    - properties属性
    - settings设置
    - typeAliases类型别名
    - typeHandlers类型处理器
    - objectFactory对象工厂
    - plugin插件
    - evironments环境
        - environment环境变量
            - transactionManager事务管理
            - dataSource数据源
    - databaseIdProvider 数据库厂商标识
    - mappers 映射器
3. 创建持久化类（POJO）和SQL映射文件
