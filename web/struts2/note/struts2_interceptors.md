#  [struts2 拦截器(interceptors)](https://struts.apache.org/core-developers/interceptors.html)
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-29 08:48

## 理解拦截器
![struts2_architecture](../picture/struts2_architecture.png "Struts2体系结构图")

### 我对拦截器的一点理解
拦截器链是通过字符串来连接的，
所谓的终止执行时在跳出方法时不执行ActionInvocation.invoke()方法
通过改变拦截器的返回值可以改变相应的访问结果(改变响应的视图)

## 配置拦截器

### 配置
定义
```
<interceptors>
    <interceptor name="myTimer" class="org.hua.struts2.interceptor.MyTimer"></interceptor>
</interceptors>
```

使用
```
<action ...>
    <interceptor-ref name="myTimer></interceptor-ref>
</action>
```
<b>PS：一旦使用了自定义的拦截器，则默认的拦截器就会失效，这时候应该把默认的拦截器栈(defaultStack)添加进去</b>

例如
struts.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>
    <constant name="struts.devMode" value="true" />
    <package name="ds" namespace="/" extends="struts-default">
        <interceptors>
            <!-- 定义拦截器 -->
            <interceptor name = "timetest" class = "org.hua.struts.action.interceptor.MytimerInterceptor" />
            <!-- 定义拦截器栈 -->
            <interceptor-stack name="interceptorStackName">
                <!--- 指定引用的拦截器栈 -->
                <interceptor-ref name="defaultStack"></interceptor-ref>
            </interceptor-stack>
        </interceptors>
        <!-- 定义默认的拦截器引用只有一个，多个的化定义成一个拦截器栈  -->
        <default-interceptor-ref name="defaultStack">
        </default-interceptor-ref>

        <action name="*User" class="org.hua.struts.action.UserAction" method="{1}">
            <interceptor-ref name="timetest"/>
            <!-- 自定义拦截器会导致默认的拦截器失效，必须重新引用默认的拦截器 -->
            <interceptor-ref name="defaultStack"/>
            <result name="login">/success.jsp</result>
            <result name="register">/success.jsp</result>
            <allowed-methods>login,register</allowed-methods>
        </action>
        <action name="login" class="org.hua.struts.action.UserAction" method="login">
            <result name="login">/success.jsp</result>
        </action>

        <action name="register" class="org.hua.struts.action.UserAction" method="register">
            <result name="register">/success.jsp</result>
        </action>
    </package>
</struts>

```

### 内置的拦截器
1. param拦截器: 将请求数据设置到Action中
2. staticParam拦截器：将配置文件中的<action>属性和<param>中的相关参数设置到对应的Action的属性中
3. servletConfig拦截器: Servlet API注入Action当中的简洁方法。
4. fileUpload拦截器：文件上传
5. validation拦截器：数据校验
6. workflow拦截器：错误终止
7. exception拦截器：异常

### 内置的拦截器栈
defaultStack拦截器栈


##  自定义拦截器
### 实现Interceptor接口
void init()
void destroy()
String intercept(ActionInvocation invocation) throws Exception
java
```
package org.hua.struts.action.interceptor;

 
/**
 * @author xinzhiniepan
 * @version 1.0
 * @since 2018-03-29 22:25:29
 */
import com.opensymphony.xwork2.interceptor.Interceptor;
import com.opensymphony.xwork2.Action;
import org.hua.struts.pojo.User;
import java.util.Map;
import com.opensymphony.xwork2.ActionInvocation;
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;
public class LoginInterceptor implements Interceptor
{
    private static final Logger logger = LogManager.getLogger();

    @Override
    public void init()
    {
        logger.info("初始化权限拦截器!");
    }

    @Override
    public void destroy()
    {
        logger.info("销毁权限拦截器!");
    }

    @Override
    public String intercept(ActionInvocation invocation) throws Exception
    {
        logger.info("开始权限拦截器!");
        Map session = invocation.getInvocationContext().getSession();
        User user = (User)session.get("user");
        if(user == null)
        {
            logger.info("返回首页!");
            return "logina"; // 一个action的result的name值, 以便完成相关操作
        }
        else
        {
            logger.info("登录成功!");
            return invocation.invoke();
        }
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
    <package name="ds" namespace="/" extends="struts-default">
        <interceptors>
            <!-- 定义拦截器 -->
            <interceptor name = "timetest" class = "org.hua.struts.action.interceptor.MytimerInterceptor" />
            <interceptor name = "authorization" class = "org.hua.struts.action.interceptor.LoginInterceptor" />
            <interceptor-stack name="mydefault">
                <interceptor-ref name="defaultStack"/>
                <interceptor-ref name="timetest"/>
            </interceptor-stack>
        </interceptors>
        <!-- 定义默认的拦截器引用  -->
        <default-interceptor-ref name="mydefault"/>

        <global-results>
            <result name="logina">/login.jsp</result>
        </global-results>

        <action name="*User" class="org.hua.struts.action.UserAction" method="{1}">
            <result name="login">/success.jsp</result>
            <result name="register">/success.jsp</result>
            <allowed-methods>login,register</allowed-methods>
        </action>
        <action name="login" class="org.hua.struts.action.UserAction" method="login">
            <result name="login">/success.jsp</result>
        </action>

        <action name="register" class="org.hua.struts.action.UserAction" method="register">
            <interceptor-ref name="authorization"/>
            <result name="register">/success.jsp</result>
        </action>
    </package>
</struts>

```

<b>PS: 根据拦截器运行的特点，可以知道拦截器只会拦截请求Action的请求。直接请求页面没有效果</b>

### 继承AbstractInterceptor抽象方法

#### 文件下载

#### 文件上传
