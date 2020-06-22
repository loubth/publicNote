# struts 2配置文件

## 配置文件的加载顺序

### 默认配置文件的加载

部分源码如下

```java
this.init_DefaultProperties();
this.init_TraditionalXmlConfigurations();
this.init_LegacyStrutsProperties();
this.init_CustomConfigurationProviders();
this.init_FilterInitParameters();
```

查看源码我们发现，struts 2加载的第一个配置文件是“`org/apache/struts2/default.properties`”，查看其内容

```properties
### This can be used to set your default locale and encoding scheme
# struts.locale=en_US
struts.i18n.encoding=UTF-8

### if specified, the default object factory can be overridden here
### Note: short-hand notation is supported in some cases, such as "spring"
###       Alternatively, you can provide a com.opensymphony.xwork2.ObjectFactory subclass name here
# struts.objectFactory = spring

### specifies the autoWiring logic when using the SpringObjectFactory.
### valid values are: name, type, auto, and constructor (name is the default)
struts.objectFactory.spring.autoWire = name

### indicates to the struts-spring integration if Class instances should be cached
### this should, until a future Spring release makes it possible, be left as true
### unless you know exactly what you are doing!
### valid values are: true, false (true is the default)
struts.objectFactory.spring.useClassCache = true
```

我们发现这其中都是一些默认常量

### xml核心配置文件和自定义配置文件的加载

接下来加载的是如下文件

```java
configPaths = "struts-default.xml,struts-plugin.xml,struts.xml";
```

其中“`struts-default.xml`”是struts本身大部分功能所使用的配置文件，其中包括拦截器。而“`struts.xml`”是我们自己编写的配置文件。

### properties自定义配置文件的加载

接下来加载的是“`struts.properties`”自定义配置文件，这个是遗留的自定义配置文件，已经被“`struts.xml`”配置文件取代

### web.xml配置文件的加载

除了tomcat会加载一次外，struts框架也会加载一次“`web.xml`”配置文件

## struts.xml配置文件基础

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
        "http://struts.apache.org/dtds/struts-2.3.dtd">
<!--注意上面的是struts文档的约束，有了这个就可以让编辑器提示-->

<!--示例-->
<struts>
    <!--包结构-->
    <package name="default" namespace="/sss" extends="struts-default">
        <!--配置Action-->
        <action name="hello" class="com.itheima.HelloAction" method="sayHello">
            <!--配置跳转界面-->
            <result name="ok">/demo/success.jsp</result>
        </action>
    </package>
</struts>
```

### package元素

#### name属性

包名，struts元素可以有多个package子元素，这些子元素的name属性不能重复

#### namespace属性

命名空间，与action的name属性组合起来构成action的访问路径，注意如果不表示根目录末尾就不用加“/”了，如上名称为hello的action的访问路径为/sss/hello.action，默认“.action”可以省略

#### extends属性

继承的元素，上面的package就继承了struts-default.xml文件中的名为struts-default的包的元素

![image-20200614133728161](image/struts%202配置文件的加载/image-20200614133728161.png)

#### abstract属性

标识该元素是被继承的

### action元素

#### name属性

与package元素的namespace属性组合起来构成action的访问路径

#### class属性

action对应的实体类，不写默认为ActionSupport类

#### method熟悉

action对应的实体类中对应的具体方法，不写默认为execute方法

### result元素

#### name属性

结果字符串名称，action返回的字符串如果和name匹配成功，那么就会跳转到该result元素对应的页面

#### type属性

跳转方式，默认为转发，也有其他方式比如重定向，具体请参照`struts-default.xml`

```xml
<package name="struts-default" abstract="true">
    <result-types>
        <result-type name="chain" class="com.opensymphony.xwork2.ActionChainResult"/>
        <result-type name="dispatcher" class="org.apache.struts2.dispatcher.ServletDispatcherResult" default="true"/>
        <result-type name="freemarker" class="org.apache.struts2.views.freemarker.FreemarkerResult"/>
        <result-type name="httpheader" class="org.apache.struts2.dispatcher.HttpHeaderResult"/>
        <result-type name="redirect" class="org.apache.struts2.dispatcher.ServletRedirectResult"/>
        <result-type name="redirectAction" class="org.apache.struts2.dispatcher.ServletActionRedirectResult"/>
        <result-type name="stream" class="org.apache.struts2.dispatcher.StreamResult"/>
        <result-type name="velocity" class="org.apache.struts2.dispatcher.VelocityResult"/>
        <result-type name="xslt" class="org.apache.struts2.views.xslt.XSLTResult"/>
        <result-type name="plainText" class="org.apache.struts2.dispatcher.PlainTextResult" />
        <result-type name="postback" class="org.apache.struts2.dispatcher.PostbackResult" />
    </result-types>
    
    ...
</package>
```

## 常量的配置

### 可以配置常量的位置

按照加载顺序先后分别为

1. struts.properties文件中（已过时）
2. struts.xml文件中
3. web.xml文件中

注意`后加载的常量会覆盖同名的先加载的常量`

具体可以设置的常量可以参考“org/apache/struts2/default.properties”文件

### struts.xml中设置常量

直接在struts根元素中写constant元素即可

```xml
<struts>
    <constant name="struts.action.extension" value="action,,"></constant>
</struts>
```

这个常量表示struts框架可以对请求url扩展名为action或没有扩展名的请求响应

### 在web.xml中设置常量

在核心过滤器中配置init-param元素

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--struts2必须配置前端控制器-->
    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
        <!--常量配置处-->
        <init-param>
            <param-name>struts.action.extension</param-name>
            <param-value>action,,</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

## 导入子配置文件

如果觉得把所有action都配置在一个配置文件里很臃肿，可以通过导入外部配置文件的方式来解决

```xml
<struts>
    <!--导入外部配置文件-->
    <include file="com/itheima/user/struts_part1.xml"/>
    <include file="com/itheima/department/struts_part2.xml"/>
</struts>
```

