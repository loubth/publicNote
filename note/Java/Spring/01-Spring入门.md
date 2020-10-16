# Spring入门

## 基础问题

### Spring框架是什么

Spring是一个为了**解决企业应用开发的复杂性问题**而创建的轻量级控制反转(IOC)和面向切面(AOP)的**开源容器框架**。Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情。然而，Spring的用途不仅限于服务器端的开发。从简单性、可测试性和松耦合的角度而言，任何Java应用都可以从Spring中受益

### Spring框架有什么用？

Spring框架可以**帮我们创建对象**，在使用Spring框架之前我们使用一个已有的类的功能需要手动在需要的地方new一个这个类的对象，这样会使这两个类耦合起来。使用spring框架后spring框架就可以帮我们在需要的地方创建对象，降低代码的耦合度

- **方便解耦，简化开发**

  Spring就是一个大工厂，可以将所有对象创建和依赖关系维护，交给Spring管理

- AOP编程的支持

  Spring提供面向切面编程，可以方便的实现对程序进行权限拦截、运行监控等功能

- 声明式事务的支持

  只需要通过配置就可以完成对事务的管理，而无需手动编程

- 方便程序的测试

  Spring对Junit4支持，可以通过注解方便的测试Spring程序

- 方便集成各种优秀框架

  Spring不排斥各种优秀的开源框架，其内部提供了对各种优秀框架（如：Struts2、Hibernate、MyBatis等）的直接支持

- 降低JavaEE API的使用难度

  Spring对JavaEE开发中非常难用的一些API（JDBC、JavaMail、远程调用等），都提供了封装，使这些API应用难度大大降低

在不使用spring框架之前，我们的service层中要使用dao层的对象，不得不在service层中new一个userDao对象。如下：

```java
//dao层对象  
public class UserDao {
    public void insert(User user) {
    }
}

//service层对象  
public class UserService {
    public void insert(User user) {
        UserDao userDao = new UserDao();        //手动new对象
        userDao.insert(user);
    }
}
```

存在的问题：层与层之间的依赖

使用框架后：（这里用的是spring中的set注入）

```java
//dao层对象  
public class UserDao {
    public void insert(User user) {
    }
}

//service层对象  
public class UserService {
    private UserDao userDao;        //Spring框架会根据配置自动帮我们new一个对象

    public UserDao getUserDao() {
        return userDao;
    }

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void insert(User user) {
        userDao.insert(user);
    }
}  
```



### Spring框架的控制反转(IOC)

IOC即Inverse Of Control，将对象的创建权有开发者反转到Spring框架，使用IOC可以解决程序耦合性高的问题

## 使用Spring框架

### 下载spring依赖库

下载地址 [https://repo.spring.io/libs-release-local/org/springframework/spring/](https://repo.spring.io/libs-release-local/org/springframework/spring/)