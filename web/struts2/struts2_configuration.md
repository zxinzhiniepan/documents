#  struts2 导入配置文件
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-27 11:38

## web.xml的配置
示例
```
...
	<filter>
		<filter-name>struts2</filter-name>
		<filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
        <!-- 自定义strust.xml配置文件的路径 -->
        <init-param>
            <param-name>config</param-name>
            <param-value>struts-default.xml,struts2/struts.xml,struts-plugin.xml</param-value>
        </init-param>
	</filter>

	<filter-mapping>
		<filter-name>struts2</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
...
```

## struts.xml配置
##### 1. [配置常量的键值对](https://github.com/apache/struts/blob/master/core/src/main/resources/org/apache/struts2/default.properties)
```
<constant name="" value=""/>
```

##### 2. package元素
属性
a. name:唯一且必须
b. extends:类似java extends(可以继承原有的包的action, result), 指定扩展的包. PS:除非有必要，否则扩展struts-default包
c. namespace: 可选属性
默认的命名空间“ namespace="" ”， 根命名空间 “ namespace="/" ”。   
<package name="test" extends="struts-default"> ，如果未指定命名空间，则命名空间默认为 “ namespace="" ”。
默认命名空间里的Action可以处理任何命名空间下的Action请求。例如，如果存在URL为/barspace/bar.action的请求，并且
/barspace的命名空间下没有名为bar的Action，
则默认命名空间下名为bar的Action也会处理用户请求。 但根命名空间下的Action只处理根命名空间下的Action的请求，
这是根命名空间和默认命名空间的区别。
命名空间只有一个级别。如果请求的URL是/bookservice/search/get.action，
系统将先在/bookservice/search的命名空间下查找名为get的Action，
如果在该命名空间内找到名为get的Action，
则由该Action处理用户的请求；如果未找到，系统将直接进入默认的命名空间中查找名为get的Action，而不会在/bookservice的命名空间下查找名为get的Action 。
d. 子元素啊action

##### 3. 拆分配置文件
使用<include file=""/>
```
<strusts>
	<include file="struts1.xml"/>
	<include file="struts2.xml"/>
	...
</strusts>
```

## Action(JAVA类)配置 
#### Action的作用
1. 封装工作单元
2. 数据转移场所
3. 返回结果字符串

#### method(action子元素)属性(同一个Action类实现不同的功能/请求)(减少Action类的代码量)(action子元素不相同)
method可以指定Action类中任何方法处理请求(方法需与execute方法一致)
通过配置action子元素中的method属性减少Action代码
PS:struts2根据action元素中method查找执行方法的两种途径
a.与method一致的方法
b.查找doMethod()形式的方法。
```java
***
    public String login()
    {
        ActionContext ac = ActionContext.getContext();
        Map<String, Object> session = ac.getSession();
        session.put("user",user);
        setSource("登录成功！");

        return "success";
    }

    public String register()
    {
        ActionContext ac = ActionContext.getContext();
        Map<String, Object> session = ac.getSession();
        session.put("user",user);
        setSource("注册成功！");
        return "success";
    }
    ...
```
struts.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>
    <constant name="struts.devMode" value="true" />
    <package name="user" namespace="" extends="struts-default">
        <action name="login" class="org.hua.struts.action.UserAction" method="login">
            <result name="success">/success.jsp</result>
        </action>

        <action name="register" class="org.hua.struts.action.UserAction" method="register">
            <result name="success">/success.jsp</result>
        </action>
    </package>
</struts>

```
注册页面
```
<%@ page contentType="text/html;Charset=UTF-8" pageEncoding="UTF-8" language = "java" %>
<%@ taglib prefix = "c" uri = "http://java.sun.com/jsp/jstl/core"%>
<%@ taglib prefix = "s" uri = "/struts-tags"%>
<%@ taglib prefix="" tagdir="/WEB-INF/tags"%>

<html lang="en">
<head>
	<meta charset="UTF-8">
    <meta http-equiv = "Content-Type" content = "text/html charset=UTF-8">
	<title>Login</title>
</head>
<body>
    <header>
        <s:form action="register" method="post">
            <s:textfield name="user.name" label="用名"/>
            <s:password name="user.password" label="密码"/>
            <s:textfield name="user.sex" label="性别"/>
            <s:textfield name="user.age" label="年龄"/>
            <s:submit value="注册"/>
        </s:form>
    </header>
</body>
</html>
```
登录页面
```
<%@ page contentType="text/html;Charset=UTF-8" pageEncoding="UTF-8" language = "java" %>
<%@ taglib prefix = "c" uri = "http://java.sun.com/jsp/jstl/core"%>
<%@ taglib prefix = "s" uri = "/struts-tags"%>
<%@ taglib prefix="" tagdir="/WEB-INF/tags"%>

<html lang="en">
<head>
	<meta charset="UTF-8">
    <meta http-equiv = "Content-Type" content = "text/html charset=UTF-8">
	<title>Login</title>
</head>
<body>
    <header>
        <s:form action="login" method="post">
            <s:textfield name="user.name" label="用名"/>
            <s:password name="user.password" label="密码"/>
            <s:submit value="登录"/>
        </s:form>
    </header>
</body>
</html>
```
#### Action中的动态方法调用(可以减少Action类和action子元素的代码量)(具有相同的action子元素)
```java
    ...
    public String login()
    {
        ActionContext ac = ActionContext.getContext();
        Map<String, Object> session = ac.getSession();
        session.put("user",user);
        setSource("登录成功！");

        return "success";
    }

    public String register()
    {
        ActionContext ac = ActionContext.getContext();
        Map<String, Object> session = ac.getSession();
        session.put("user",user);
        setSource("注册成功！");
        return "success";
    }
    ...
```
struts.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>
    <constant name="struts.devMode" value="true" />
    <package name="user" namespace="" extends="struts-default">
        <action name="login" class="org.hua.struts.action.UserAction" method="login">
            <result name="success">/success.jsp</result>
        </action>

        <action name="register" class="org.hua.struts.action.UserAction" method="register">
            <result name="success">/success.jsp</result>
        </action>
    </package>
</struts>

```
注册页面
```
<%@ page contentType="text/html;Charset=UTF-8" pageEncoding="UTF-8" language = "java" %>
<%@ taglib prefix = "c" uri = "http://java.sun.com/jsp/jstl/core"%>
<%@ taglib prefix = "s" uri = "/struts-tags"%>
<%@ taglib prefix="" tagdir="/WEB-INF/tags"%>

<html lang="en">
<head>
	<meta charset="UTF-8">
    <meta http-equiv = "Content-Type" content = "text/html charset=UTF-8">
	<title>Login</title>
</head>
<body>
    <header>
        <s:form action="register" method="post">
            <s:textfield name="user.name" label="用名"/>
            <s:password name="user.password" label="密码"/>
            <s:textfield name="user.sex" label="性别"/>
            <s:textfield name="user.age" label="年龄"/>
            <s:submit value="注册"/>
        </s:form>
    </header>
</body>
</html>
```
登录页面
```
<%@ page contentType="text/html;Charset=UTF-8" pageEncoding="UTF-8" language = "java" %>
<%@ taglib prefix = "c" uri = "http://java.sun.com/jsp/jstl/core"%>
<%@ taglib prefix = "s" uri = "/struts-tags"%>
<%@ taglib prefix="" tagdir="/WEB-INF/tags"%>

<html lang="en">
<head>
	<meta charset="UTF-8">
    <meta http-equiv = "Content-Type" content = "text/html charset=UTF-8">
	<title>Login</title>
</head>
<body>
    <header>
        <s:form action="login" method="post">
            <s:textfield name="user.name" label="用名"/>
            <s:password name="user.password" label="密码"/>
            <s:submit value="登录"/>
        </s:form>
    </header>
</body>
</html>
```

#### Action
```java
<action name="student*" class="com.itmyhome.StudentAction" method="{1}">  
     <result name="{1}">/student{1}.jsp</result>  
</action>  
```

```
<a href="studentadd">  新增学生</a>  
<a href="studentdelete"> 删除学生</a>  
```
studentadd就会调用StudentAction中的add方法 然后跳转到studentadd.jsp
studentdelete就会调用StudentAction中的delete方法 然后跳转到studentdelete.jsp

                     Struts2支持动态方法调用，它指的是一个Action中有多个方法，系统根据表单元素给定的action来访问不同的方法，而不用写多个Action。

## 配置默认的Action
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>
    <constant name="struts.devMode" value="true" />
    <package name="user" namespace="" extends="struts-default">
        <default-action-ref name="login"/>
        <action name="login" class="org.hua.struts.action.UserAction" method="login">
            <result name="success">/success.jsp</result>
        </action>

        <action name="register" class="org.hua.struts.action.UserAction" method="register">
            <result name="success">/success.jsp</result>
        </action>
    </package>
</struts>


```

## Result配置
1. dispatcher: 转发,保留请求信息
2. redirect:重定向页面, 不保留请求信息
3. redirectAction: 重定向请求一个新的Action, 不保留请求信息

## 动态结果
```java
...
	public void login()
	{
		String k = "";
		if(k==1)
		{
			k="manager";	
		}
		else
		{
			k="common";	
		}
		return k;
	}
	public void manager()
	{
		return "manager";
	}

	public void common()
	{
		return "common";
	}
...
```

struts.xml
```
    <package name="usera" namespace="" extends="struts-default">
        <action name="login" class="org.hua.struts.action.UserAction" method="login">
			<!-- 读取action中的k值 -->
            <result name="login">${k}</result>
        </action>

        <action name="common" class="org.hua.struts.action.UserAction" method="register">
            <result name="register">/success.jsp</result>
        </action>
        <action name="manager" class="org.hua.struts.action.UserAction" method="register">
            <result name="register">/success.jsp</result>
        </action>
    </package>
```

## 全局结果
struts.xml
```
<struts>  
     <constant name="struts.devMode" value="true" />  
     <package name="user" namespace="/user" extends="struts-default">  
     <!-- package下的action共用global里面的result方法 -->  
        <global-results>  
            <result name="mainPage">/Main.jsp</result>  
        </global-results>  
        <action name="user" class="com.java.action.User">  
            <result name="success">/Success.jsp</result>  
            <result name="error">/Error.jsp</result>  
        </action>   
     </package>  
     <package name="admin" namespace="/admin" extends="user"><!-- 继承user的配置 -->  
        <action name="admin" class="com.java.admin.AdminAction">  
            <result name="admin">/admin.jsp</result>  
        </action>  
     </package>  
 </struts>
```
