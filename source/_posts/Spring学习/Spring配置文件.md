---
title: Spring配置文件
hidden: false
categories: Spring学习
tags:
  - Spring
  - Java
abbrlink: 84072a14
date: 2026-04-05 19:03:38
---
#### 一、Bean 标签

```xml
<bean id="userDao" class="kk3twt.implement1.UserDaoImplement"></bean>
```

`Bean` 标签用于配置对象，默认情况下调用的是类中的无参构造函数（没有无参构造函数就不能调用成功）

`Bean` 标签的基本属性

-  `id` 标识：`Bean` 的唯一性标识，在配置文件中不允许重复

-  `class` 标识：表示 `Bean` 的全限定名

#### 二、Bean 的范围配置

`Bean` 标签使用 `scope` 标识配置范围，

```xml
<bean id="userDao" class="kk3twt.implement1.UserDaoImplement" scope="singleton"></bean>
```

`scope` 的取值如下：

| 取值范围       | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| singleton      | 默认值，单例的，表示 Bean 的对象在类中只会构造（创建）一次   |
| prototype      | 多例的，表示 Bean 的对象在类中可以构造（创建）多次           |
| request        | （WEB项目）Spring创建一个 Bean 的对象，并将对象存入 request 域中 |
| session        | （WEB项目）Spring创建一个 Bean 的对象，并将对象存入 session 域中 |
| global session | （WEB项目）应用在 Portlet 环境，如果没有该环境，那么 global session 相当于 session |

需要说明的是，**使用 `singleton` 配置 `scope` 的时候**，`Bean` 的对象在 `ApplicationContext` 对象创建时创建

**而使用 `prototype` 配置 `scope` 的时候**，`Bean` 的对象在执行 `getBean` 方法之后才会创建，而且执行一次 `getBean` 就创建一个对象

#### 三、Bean 的生命周期配置

如果在 `Bean` 的类中定义了初始化或销毁方法，可以使用 `init-method` 标签和 `destroy-method` 标签指明对应的方法

此时创建对象时会自动执行初始化方法，而在销毁对象时执行销毁方法

```xml
<bean id="userDao" class="kk3twt.implement1.UserDaoImplement"
      init-method="init" destroy-method="destroy"></bean>
```

#### 四、Bean实例化的三种方式

- 无参构造方法（默认）

- 工厂静态方法

- 工厂实例方法

**使用工厂静态方法实例化 `Bean` 时**，需要先创建静态工厂类（如 `StaticFactory` ），并在这个类里编写将Bean实例化的方法（如 `getDao` ）

```java
public class StaticFactory {
    public static UserDao getDao() {
        return new UserDaoImpl();
    }
}
```

然后使用 `factory-method` 标签指定工厂静态方法，并将 `class` 标签中的值改为刚刚创建的静态工厂类

```xml
<bean id="userDao" class="kk3twt.factory.StaticFactory" factory-method="getDao"></bean>
```

而对于工厂实例方法，同样需要创建工厂类（但不是静态类），并编写将 `Bean` 实例化的方法

但是在配置文件xml中，需要先声明工厂类对应的 `Bean`（如 `Factory`），然后在需要使用的 `Bean` 中将 `class` 标签替换为 `factory-bean` 标签，然后再配置 `factory-method` 

```xml
<bean id="factory" class="kk3twt.factory.DynamicFactory"></bean>
<bean id="userDao" factory-bean="Factory" factory-method="getDao"></bean>
```
