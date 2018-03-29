#  [struts2 拦截器(interceptors)](https://struts.apache.org/core-developers/interceptors.html)
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-29 08:48

## 理解拦截器
![struts2_architecture](../picture/struts2_architecture.png "Struts2体系结构图")[Struts2体系结构图]

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
        <!-- 定义默认的拦截器引用  -->
        <default-interceptor-ref>
                <interceptor-ref name="defaultStack"></interceptor-ref>
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
### 继承AbstractInterceptor抽象方法


### 实现Interceptor接口
void init()
void destroy()
String intercept(ActionInvocation invocation) throws Exception
