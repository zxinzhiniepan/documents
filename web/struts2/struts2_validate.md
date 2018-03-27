#  struts 数据校验
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-27 11:14

## 校验
Action类需要继承com.opensymphony.xwork2.ActionSupport类;
执行validate()方法中添加了校验信息，则将会根据Action的配置跳转到name为input的视图去。

## [struts2标签](https://struts.apache.org/tag-developers/tag-reference.html)
### UI标签
### 通用标签

## 代码
```java
package org.hua.struts.action;
 
/**
 * @author xinzhiniepan
 * @version 1.0
 * @since 2018-03-26 16:28:07
 */
 
import com.opensymphony.xwork2.ActionSupport;
import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import org.apache.struts2.interceptor.SessionAware;
import org.apache.struts2.interceptor.ServletRequestAware;
import org.apache.struts2.ServletActionContext;
import javax.servlet.http.HttpSession;
import javax.servlet.http.HttpServletRequest;
import java.util.Map;
public class Login extends ActionSupport implements ServletRequestAware
{
    private String name;
    private String password;
    private String message;
    private HttpServletRequest request;

    @Override
    public void validate()
    {
        if(name==null || name.length()==0)
        {
            addFieldError("name", "用户名不能为空");
        }
        else if(password==null || password.length()== 0)
        {
            addFieldError("password", "密码不能为空");
        }
    }

    @Override
    public String execute()
    {
        /*
        ActionContext ac = ActionContext.getContext();
        Map<String, Object> request = (Map)ac.get("request");
        Map<String, Object> session = ac.getSession();
        Map<String, Object> application = ac.getApplication();
        */
        HttpSession session =  request.getSession();
        if(name.equals("zhanghua") && password.equals("123456"))
        {
            session.setAttribute("user", name);
            session.setAttribute("pwd", password);
            return Action.SUCCESS;
        }
        else
        {
            session.setAttribute("user", name);
            session.setAttribute("pwd", password);
            return Action.ERROR;
        }
    }


    public void setServletRequest(HttpServletRequest request)
    {
        this.request = request;
    }

    public void setName(String name)
    {
        this.name = name;
    }

    public String getName()
    {
        return name;
    }

    public void setPassword(String password)
    {
        this.password = password;
    }

    public String getPassword()
    {
        return password;
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

JSP页面
```jsp
<%@ page contentType="text/html;Charset=UTF-8" pageEncoding="UTF-8" language = "java" %>
<%@ taglib prefix = "c" uri = "http://java.sun.com/jsp/jstl/core"%>
<%@ taglib prefix = "s" uri = "/struts-tags"%>
<%@ taglib prefix="" tagdir="/WEB-INF/tags"%>

<html lang="en">
<head>
	<meta charset="UTF-8">
    <meta http-equiv = "Content-Type" content = "text/html charset=UTF-8">
	<title>practice struts2</title>
</head>
<body>
    <header>
        <h1><s:property value="message"/></h1>
    </header>

    <s:fielderror name="name"/>
    <s:fielderror name="password"/>
    <article>
        <s:form action = "test" method = "post">
            <div>
                <s:textfield name="name" label="用户"/>
            </div>
            <div>
                <s:password name="password" label="密码"/>
            </div>
            <div>
                <s:submit value="提交"/>
            </div>
            <div>
        </s:form>
    </article>

    <footer>
    </footer>
</body>
</html>
```
