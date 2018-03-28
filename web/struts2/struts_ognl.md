#  对象图导航语言
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-28 08:54

## OGNL在框架中的作用

## 类型转换
#### 内置转换器
#### 自定义转换器
1. 创建
继承基类(org.apache.struts2.util.StrutsTypeConverter)
重写两个方法：
public Object convertFromString(Map context, String[] values, Class toType)
public String convertToString(Map context, Object)
    
2. 配置
a. 应用于全局
编写xwort-conversion.properties放在src/resources等classpath目录下
b. 应用与特定
编写ClassName-conversion.properties放置在特定应用的类的目录下

## 表达式
### 表达式语言
1. 访问bean的属性
2. 访问集合对象

### 访问ActionContext中的数据

### 查看ActionContext中的数据
在页面中加入标签：
```jsp
<s:debug/>
```
### Struts2标签和OGNL表达式

## 标签
### URL标签 
### 日期标签
