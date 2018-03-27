#  struts2中的共享数据传递
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-27 08:32

# [传递对象参数](https://blog.csdn.net/qq_24448899/article/details/76339443)
## Struts2能传递哪些参数？
在Struts2中，框架替我们做了很多便利的事，比如，我们可以通过表单组件的name，来决定传递的数据在Action中被解析成普通参数、对象、List、还是Map

## 传递形式
Action中要获取页面传递过来的参数，只需要在Action中定义成员变量并且提供设置器(Setter)和获取器(Getter)，Struts2就会帮我们把数据封装进去。传递对象也一样，页面中填的依旧是字符串，只不过到了Action之后框架会根据参数的名称name来判读要“组装”成什么样的参数。

## 代码
下面就以Person，List<Person>, Map<String, Person>接收参数为例：

Person.java

```java
package action;

import java.io.Serializable;

public class Person implements Serializable{
    @Override
    public String toString() {
        return "Person [name=" + name + ", age=" + age + "]";
    }
    private String name;
    private int age;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
}
```

ArgsTestAction.java
```
package action;

import java.util.List;
import java.util.Map;

import com.opensymphony.xwork2.ActionSupport;

public class ArgsTestAction extends ActionSupport {
    //装参数的变量们
    private Person person;
    private List<Person> personList;
    private Map<String,Person> personMap;

    public String comeOn(){
        System.out.println(person.toString());

        System.out.println("\nlist of person:");
        for(Person p : personList){
            System.out.println(p);
        }

        System.out.println("\nmap of person:");
        for(String key : personMap.keySet()){
            System.out.println(personMap.get(key));
        }
        return SUCCESS;
    }
    public Person getPerson() {
        return person;
    }

    public void setPerson(Person person) {
        this.person = person;
    }

    public List<Person> getPersonList() {
        return personList;
    }

    public void setPersonList(List<Person> personList) {
        this.personList = personList;
    }

    public Map<String, Person> getPersonMap() {
        return personMap;
    }

    public void setPersonMap(Map<String, Person> personMap) {
        this.personMap = personMap;
    }
}
```

配置文件struts.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
    "http://struts.apache.org/dtds/struts-2.3.dtd">
<struts>
    <!-- namespace要用"/" action不用-->
    <package name="test" extends="struts-default" namespace="/test">
        <action name="comeOn" class="action.ArgsTestAction" method="comeOn">
            <result name="success">/success.jsp</result>
        </action>

    </package>
</struts>
```

输入页面input.jsp
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
    <form action="${pageContext.request.contextPath }/test/comeOn" method="post">
        <!-- Person对象传递，使用"Action定义的对象名.对象属性"形式获取 -->
        用户1 >>>姓名：<input type="text" name="person.name">年龄：<input type="text" name="person.age"><br>

        <!-- List对象传递，使用"Action定义的List名.[坐标].对象属性"形式获取 -->
        用户2 >>>姓名：<input type="text" name="personList[0].name">年龄：<input type="text" name="personList[0].age"><br>
        用户3 >>>姓名：<input type="text" name="personList[1].name">年龄：<input type="text" name="personList[1].age"><br>

        <!-- Map对象传递，使用"Action定义的Map名.[key].对象属性"形式获取 -->
        用户4 >>>姓名：<input type="text" name="personMap['no1'].name">年龄：<input type="text" name="personMap['no1'].age"><br>
        用户5 >>>姓名：<input type="text" name="personMap['no2'].name">年龄：<input type="text" name="personMap['no2'].age"><br>
        <input type="submit">
    </form>
</body>
</html>
```

# 共享数据
## 一、解耦合
#### 类
java代码
```
package org.hua.struts.action;

 
/**
 * @author xinzhiniepan
 * @version 1.0
 * @since 2018-03-26 13:45:54
 */
import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import java.util.Map;
public class Test implements Action
{
    private String name;
    private String passwrod;
    private String message;

    public void setName(String name)
    {
        this.name = name;
    }

    public String execute()
    {
        ActionContext ac = ActionContext.getContext();
        // request(没有直接获取request的方法)
        Map<String,Object> request = (Map<String, Object>)ac.get("request");
        // application
        Map<String, Object> application = ac.getApplication();
        // session
        Map<String, Object> session = ac.getSession();
        switch(name)
        {
            case "request":request.put("request", "request");
                           break;
            case "session":session.put("session","session");
                           break;
            case "application":application.put("application","application");
                               break;
            default:
                               return "error";
        }
        return Action.SUCCESS;
    }

    public String getName()
    {
        return name;
    }

    public void setPassword(String passwrod)
    {
        this.passwrod = passwrod;
    }

    public String getPassword()
    {
        return passwrod;
    }

    public void setMessage(String message)
    {
        this.message = message;
    }
    public String getMessage()
    {
        return message;
    }
}
```

struts.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

    <constant name="struts.devMode" value="true" />

    <package name="basicstruts2" namespace="/" extends="struts-default">
        <action name="test"
			class="org.hua.struts.action.Test">
            <result name="success">/success.jsp</result>
            <result name="error">/fail.jsp</result>
        </action>
    </package>

    <package name="login" namespace="/" extends="struts-default">
        <action name="login"
			class="org.hua.struts.action.Login" >
            <result name="success">/success.jsp</result>
            <result name="error">/fail.jsp</result>
            <result name="input">/index.jsp</result>
        </action>
    </package>
</struts>
```

#### 注入Servlet API对象到Action的Map中
通过接口向Action实例注入HttpSession,HttpServletRequest,ServletContext对应的Map
三个接口
org.apache.struts2.interceptor.RequestAware
org.apache.struts2.interceptor.SessionAware;
org.apache.struts2.interceptor.Application;
java Code
```
package org.hua.struts.action;

 
/**
 * @author xinzhiniepan
 * @version 1.0
 * @since 2018-03-26 13:45:54
 */
import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import org.apache.struts2.interceptor.RequestAware;
import org.apache.struts2.interceptor.SessionAware;
import org.apache.struts2.interceptor.ApplicationAware;
import java.util.Map;
import java.util.HashMap;
public class Test implements Action,RequestAware,SessionAware,ApplicationAware
{
    private String name;
    private String passwrod;
    private String message;
    private Map<String, Object> request;
    private Map<String, Object> session;
    private Map<String, Object> application;

    public void setName(String name)
    {
        this.name = name;
    }

    public String execute()
    {
        Map<String,Object> temp = new HashMap();
        switch(name)
        {
            case "request":
                temp.put("request", "request");
                setRequest(temp);
                break;
            case "session":
                temp.put("session","session");
                setSession(temp);
                break;
            case "application":
                temp.put("application","application");
                setApplication(temp);
                break;
            default:
                return "error";
        }
        return Action.SUCCESS;
    }

    public void setRequest(Map<String,Object> request)
    {
        this.request = request;
    }

    public void setSession(Map<String,Object> session)
    {
        this.session = session;
    }

    public void setApplication(Map<String,Object> application)
    {
        this.application = application;
    }

    public String getName()
    {
        return name;
    }

    public void setPassword(String passwrod)
    {
        this.passwrod = passwrod;
    }

    public String getPassword()
    {
        return passwrod;
    }

    public void setMessage(String message)
    {
        this.message = message;
    }
    public String getMessage()
    {
        return message;
    }
}
```
struts.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

    <constant name="struts.devMode" value="true" />

    <package name="basicstruts2" namespace="/" extends="struts-default">
        <action name="test"
			class="org.hua.struts.action.Test">
            <result name="success">/success.jsp</result>
            <result name="error">/fail.jsp</result>
        </action>
    </package>

    <package name="login" namespace="/" extends="struts-default">
        <action name="login"
			class="org.hua.struts.action.Login" >
            <result name="success">/success.jsp</result>
            <result name="error">/fail.jsp</result>
            <result name="input">/index.jsp</result>
        </action>
    </package>
</struts>
```

## 二、耦合
#### 使用对象
org.apache.struts2.ServletActionContext;
HttpSession：ServletActionContext.getRequest().getSession();
```
package org.hua.struts.action;

 
/**
 * @author xinzhiniepan
 * @version 1.0
 * @since 2018-03-26 13:45:54
 */
import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import org.apache.struts2.ServletActionContext;
import javax.servlet.http.HttpSession;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.ServletContext;
public class Test implements Action
{
    private String name;
    private String passwrod;
    private String message;

    public void setName(String name)
    {
        this.name = name;
    }

    public String execute()
    {
        HttpSession session = ServletActionContext.getRequest().getSession();
        ServletContext application = ServletActionContext.getServletContext();
        HttpServletRequest request = ServletActionContext.getRequest();
        switch(name)
        {
            case "session" :
                session.setAttribute("namea","session");
                break;
            case "application":
                application.setAttribute("namea","application");
                break;
            case "request":
                request.setAttribute("namea","request");
                break;
        }
        return Action.SUCCESS;
    }

    public String getName()
    {
        return name;
    }

    public void setPassword(String passwrod)
    {
        this.passwrod = passwrod;
    }

    public String getPassword()
    {
        return passwrod;
    }

    public void setMessage(String message)
    {
        this.message = message;
    }
    public String getMessage()
    {
        return message;
    }
}
```

struts.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

    <constant name="struts.devMode" value="true" />

    <package name="basicstruts2" namespace="/" extends="struts-default">
        <action name="test"
			class="org.hua.struts.action.Test">
            <result name="success">/success.jsp</result>
            <result name="error">/fail.jsp</result>
        </action>
    </package>

    <package name="login" namespace="/" extends="struts-default">
        <action name="login"
			class="org.hua.struts.action.Login" >
            <result name="success">/success.jsp</result>
            <result name="error">/fail.jsp</result>
            <result name="input">/index.jsp</result>
        </action>
    </package>
</struts>
```

#### 使用注入方式
org.apache.struts2.util.ServletContextAware;
org.apache.struts2.interceptor.ServletRequestAware;
org.apache.struts2.interceptor.ServletResponseAware;
java code
```java
package org.hua.struts.action;

 
/**
 * @author xinzhiniepan
 * @version 1.0
 * @since 2018-03-26 13:45:54
 */
import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import org.apache.struts2.ServletActionContext;
import javax.servlet.http.HttpSession;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.ServletContext;
import org.apache.struts2.util.ServletContextAware;
import org.apache.struts2.interceptor.ServletRequestAware;
import org.apache.struts2.interceptor.ServletResponseAware;
public class Test implements Action, ServletContextAware, ServletRequestAware
{
    private String name;
    private String passwrod;
    private String message;
    private ServletContext application;
    private HttpServletRequest request;

    public void setName(String name)
    {
        this.name = name;
    }

    public String execute()
    {
        //ServletContext application = null;
        //HttpServletRequest request = null;
        HttpSession session = request.getSession();
        switch(name)
        {
            case "session" :
                session.setAttribute("namea","session");
                setServletRequest(request);
                break;
            case "application":
                application.setAttribute("namea","application");
                setServletContext(application);
                break;
            case "request":
                request.setAttribute("namea","request");
                setServletRequest(request);
                break;
        }
        return Action.SUCCESS;
    }

    public void setServletContext(ServletContext application)
    {
        this.application = application;
    }
    public void setServletRequest(HttpServletRequest request)
    {
        this.request=request;
    }

    public String getName()
    {
        return name;
    }

    public void setPassword(String passwrod)
    {
        this.passwrod = passwrod;
    }

    public String getPassword()
    {
        return passwrod;
    }

    public void setMessage(String message)
    {
        this.message = message;
    }
    public String getMessage()
    {
        return message;
    }
}
```

struts.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

    <constant name="struts.devMode" value="true" />

    <package name="basicstruts2" namespace="/" extends="struts-default">
        <action name="test"
			class="org.hua.struts.action.Test">
            <result name="success">/success.jsp</result>
            <result name="error">/fail.jsp</result>
        </action>
    </package>

    <package name="login" namespace="/" extends="struts-default">
        <action name="login"
			class="org.hua.struts.action.Login" >
            <result name="success">/success.jsp</result>
            <result name="error">/fail.jsp</result>
            <result name="input">/index.jsp</result>
        </action>
    </package>
</struts>
```
