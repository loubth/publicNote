# action的编写和访问

## action的编写方式

请先确保已经学会了“[struts 2入门](01-struts%202入门.md)”

### POJO类方式

POJO类简单来说就是不实现任何接口不继承任何类的类

> 百度百科：POJO（Plain Ordinary Java Object）简单的Java对象，实际就是普通JavaBeans，是为了避免和EJB混淆所创造的简称。

```java
public class Demo1Action {
    public String execute() {	//默认方法名execute
        System.out.println("POJO形式的action运行...");
        return null;    //返回null代表不跳转
    }
}
```

```xml
<struts>
    <!--包结构-->
    <package name="default" namespace="/" extends="struts-default">
        <!--配置Action-->
        <action name="demo1" class="com.itheima.Demo1Action"></action>
    </package>
</struts>
```

### 实现Action接口方式

Action接口源码，其中包含五个跳转结果常量

```java
public interface Action {
    String SUCCESS = "success";
    String NONE = "none";
    String ERROR = "error";
    String INPUT = "input";
    String LOGIN = "login";

    String execute() throws Exception;
}
```

```java
public class Demo2Action implements Action {
    @Override
    public String execute() throws Exception {
        System.out.println("实现Action接口形式的action运行...");
        return SUCCESS; //和返回一个“success”是一回事
    }
}
```

```xml
<struts>
    <!--包结构-->
    <package name="default" namespace="/" extends="struts-default">
        <!--配置Action-->
        <action name="demo2" class="com.itheima.Demo2Action">
            <result name="success">/demo/demo.jsp</result>
        </action>
    </package>
</struts>
```

### 继承ActionSupport类方式（**推荐方式**）	

ActionSupport类也是实现了Action接口的类，另外它还有许多其它的方法，具体可以去查看源码

```java
public class Demo3Action extends ActionSupport {
    @Override
    public String execute() throws Exception {
        System.out.println("继承ActionSupport形式的action运行...");
        return SUCCESS;
    }
}
```

```xml
<struts>
    <!--包结构-->
    <package name="default" namespace="/" extends="struts-default">
        <!--配置Action-->
        <action name="demo3" class="com.itheima.Demo3Action">
            <result name="success">/demo/demo.jsp</result>
        </action>
    </package>
</struts>
```

## action的访问

action的访问路径配置可以参见[struts 2入门](01-struts 2入门.md)

**提示**：action的访问路径有容错机制

比如：我们配置某个package的namespace="/simple"，在这个package里面有一个action的name是hello，那么，我们可以通过：“/simple/hello.action”或“/simple/a/b/c/hello.action”等等都可以访问到这个action。这主要因为是在struts 2中，对于访问的路径有一种容错能力，可以根据路径依次查找相应的package中的action

### 传统的配置方式

传统的配置方式就是为每个请求都配置一条，比如有两个action，一个是“保存用户”，一个是“删除用户”

```java
public class DemoAction extends ActionSupport {
    public String save() {
        System.out.println("保存用户...");
        return SUCCESS;
    }

    public String delete() {
        System.out.println("删除用户...");
        return SUCCESS;
    }
}
```

那我们就需要在struts.xml中配置两条action

```xml
<struts>
    <!--包结构-->
    <package name="default" namespace="/" extends="struts-default">
        <!--保存Action-->
        <action name="saveCust" class="com.itheima.DemoAction" method="save">
            <result name="success">/demo/demo.jsp</result>
        </action>
        <!--删除Action-->
        <action name="delCust" class="com.itheima.DemoAction" method="delete">
            <result name="success">/demo/demo.jsp</result>
        </action>
    </package>
</struts>
```

如此操作后，我们可以在页面访问

```html
<a href="${pageContext.request.contextPath}/saveCust.action">保存用户</a><br>
<a href="${pageContext.request.contextPath}/delCust.action">删除用户</a>
```

### 通配符配置方式

为了简化我们的配置方式，我们在配置文件中可以采用通配符“\*”来匹配多个action。我们可以把“\*”放在action元素的name属性中，然后在method属性中使用大括号和数字的方式代表第n个“\*”匹配到的字符串

比如我们有如下两个action处理方法

```java
public class DemoAction extends ActionSupport {
    public String save() {
        System.out.println("保存用户...");
        return "saveOK";
    }

    public String delete() {
        System.out.println("删除用户...");
        return "delOK";
    }
}
```

我们的访问页面为

```html
<a href="${pageContext.request.contextPath}/cust_save.action">保存用户</a><br>
<a href="${pageContext.request.contextPath}/cust_delete.action">删除用户</a>
```

那我们就可以这样配置使页面和action处理方法联系起来

```xml
<struts>
    <!--包结构-->
    <package name="default" namespace="/" extends="struts-default">
        <!--配置Action-->
        <action name="cust_*" class="com.itheima.DemoAction" method="{1}">
            <result name="saveOK">/demo/demo.jsp</result>
            <result name="delOK">/demo/demo.jsp</result>
        </action>
    </package>
</struts>
```

### 动态方法绑定配置方式

所谓动态方法绑定，就是在用户界面直接配置要访问的方法

首先动态方法绑定配置方式在struts框架中是默认关闭的，需要手动配置相关常量打开，我们可以查看源配置文件“default.properties”

```properties
struts.enable.DynamicMethodInvocation = false
```

我们需要在struts.xml配置文件中将其置为true

```xml
<struts>
    <constant name="struts.enable.DynamicMethodInvocation" value="true"/>
    <!--...-->
</struts>
```

接下来就是关键了，我们需要在访问action的url中直接指定action对应的处理类的具体方法名，要求与action的名字用英文感叹号隔开，如下所示，两个url分别对应处理类的save方法和delete方法

```jsp
<a href="${pageContext.request.contextPath}/cust!save.action">保存用户</a><br>
<a href="${pageContext.request.contextPath}/cust!delete.action">删除用户</a>
```

由于已经指定了具体的方法名，因此在struts配置文件中action元素就不需要指定method属性了

```xml
<struts>
    <constant name="struts.enable.DynamicMethodInvocation" value="true"/>
    <!--包结构-->
    <package name="default" namespace="/" extends="struts-default">
        <!--配置Action-->
        <action name="cust" class="com.itheima.DemoAction">
            <result name="saveOK">/demo/demo.jsp</result>
            <result name="delOK">/demo/demo.jsp</result>
        </action>
    </package>
</struts>
```

