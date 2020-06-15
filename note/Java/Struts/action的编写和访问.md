# action的编写和访问

## action的编写方式

请先确保已经学会了“[struts 2入门](struts 2入门.md)”

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

### 继承ActionSupport类方式