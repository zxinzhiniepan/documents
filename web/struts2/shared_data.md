#  struts2中的共享数据
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-27 08:32

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
