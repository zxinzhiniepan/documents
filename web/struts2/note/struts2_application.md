#  struts2的应用
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-26 08:25

## 创建一个struts2的[标准步骤](https://struts.apache.org/getting-started/how-to-create-a-struts2-web-application.html)
1. 创建web项目
2. 创建页面(jsp)
3. 搭建struts2的运行环境(导入jar包到classpath)
4. 配置日志记录
5. 添加Struts2过滤器
6. 创建struts.xml配置文件
7. 编译启动服务器

## 一、创建web项目
使用gradle.build创建
使用maven创建

## 二、创建页面(jsp)
创建jsp文件

## 三、搭建struts2的运行环境(导入jar包到classpath)
导入jar包
```
// strust2框架的核心类库
compile group: 'org.apache.struts', name: 'struts2-core', version: '2.5.16'
// XWork类库
compile group: 'org.apache.struts.xwork', name: 'xwork-core', version: '2.3.34'
// Struts2使用的一种表达式语言类库
compile group: 'ognl', name: 'ognl', version: '3.2.6'
// Struts2标签模板使用类库
compile group: 'org.freemarker', name: 'freemarker', version: '2.3.27-incubating'
// 字节吗处理
compile group: 'org.javassist', name: 'javassist', version: '3.22.0-GA'
// 文件上传
compile group: 'commons-fileupload', name: 'commons-fileupload', version: '1.3.3'
// javaIO扩展
compile group: 'commons-io', name: 'commons-io', version: "2.6" //web文件上传IO
// 数据类型工具
compile group: 'org.apache.commons', name: 'commons-lang3', version: '3.7'

```

## 四、配置日志记录
配置log4j2.xml文件
```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.opensymphony.xwork2" level="debug"/>
        <Logger name="org.apache.struts2" level="debug"/>
        <Root level="warn">
            <AppenderRef ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

## 五、添加Struts2过滤器
配置src/main/webapp/WEB-INF/文件夹中的web.xml文件
```xml
...
	<filter>
		<filter-name>struts2</filter-name>
		<filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
	</filter>

	<filter-mapping>
		<filter-name>struts2</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
...
```

## 六、创建struts.xml配置文件
struts2.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

    <constant name="struts.devMode" value="true" />

    <package name="basicstruts2" extends="struts-default">
        <action name="index">
            <result>/index.jsp</result>
        </action>
    </package>

</struts>
```

## 七、编译启动服务器
