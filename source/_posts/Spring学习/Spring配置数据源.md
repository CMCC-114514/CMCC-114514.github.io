---
title: Spring-配置数据源
hidden: false
categories: 
  - 学习记录
  - Spring框架
tags:
  - Spring
  - Java
abbrlink: 4364fdb7
date: 2026-05-01 17:04:50
---
## 数据源使用步骤

- 导入数据源坐标和数据库驱动坐标
- 创建数据源对象
- 设置数据源的基本连接数据（驱动，地址，用户名，密码）
- 获取连接资源和归还资源

## 创建数据源

这里选择的数据源为 `c3p0` 和 `druid` ，并使用 `Junit` 进行数据源的测试

### 在 `test.java` 中创建数据源对象

#### 1. 在 `pom.xml` 中导入数据源坐标

在xml中添加 `<dependencies>` 标签，然后在里面添加相关的数据源：

```xml
<dependencies>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</art
        <version>5.1.32</version>
    </dependency>
    <dependency>
        <groupId>c3p0</groupId>
        <artifactId>c3p0</artifactId>
        <version>0.9.1.2</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.10</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

#### 2. 在 `test.java` 中创建数据源对象

可以按如下方式创建数据源对象：

```java
import com.alibaba.druid.pool.DruidDataSource;
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.Test;

import java.sql.Connection;

public class JunitTest {
    @Test
    // 测试C3P0数据源
    public void test1() throws Exception {
        ComboPooledDataSource cpd = new ComboPooledDataSource();    // 创建数据源对象
        cpd.setDriverClass("com.mysql.jdbc.Driver");                // 设置数据源驱动
        cpd.setJdbcUrl("jdbc:mysql://localhost:3306/test");         // 设置数据源地址
        cpd.setUser("root");                                        // 设置用户名
        cpd.setPassword("public");                                  // 设置密码
        Connection connection = cpd.getConnection();                // 建立连接
        System.out.println(connection);
        connection.close();                                         // 关闭连接
    }

    @Test
    // 测试druid数据源
    public void test2() throws Exception {
        DruidDataSource dd = new DruidDataSource();					// 创建数据源对象
        dd.setDriverClassName("com.mysql.jdbc.Driver");				// 设置数据源驱动
        dd.setUrl("jdbc:mysql://localhost:3306/test");				// 设置数据源地址
        dd.setUsername("root");										// 设置用户名
        dd.setPassword("public");									// 设置密码
        Connection connection = dd.getConnection();					// 建立连接
        System.out.println(connection);
        connection.close();											// 关闭连接
    }
}
```

### 通过Spring容器创建数据源对象

在上文的配置数据源的代码中，出现了很多 `set***()` 的方法，而创建数据源也就是创建数据源的对象，所以与一般的使用 Spring 创建对象一样，我们也可以通过 Spring 容器创建数据源类的对象

#### 1. 在 `ApplicationContext.xml` 中配置参数

以创建 `c3p0` 数据源对象为例，在 `Context.xml` 文件中按如下方式配置各个参数：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"/>
        <property name="user" value="root"/>
        <property name="password" value="public"/>
    </bean>

</beans>
```

#### 2. 在方法中创建 `ApplicationContext` 对象并获取数据源对象

通过 `ClassPathXmlApplicationContext("ApplicationContext.xml")` 通过 xml 文件创建对象，然后通过 `getBean()` 方法获取 `c3p0` 数据源对象并建立连接：

```java
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.sql.Connection;

public class JunitTest {
    @Test
    // 测试C3P0数据源，数据源类通过Spring容器加载
    public void test4() throws Exception {
        // 通过Spring容器获取数据源类
        ApplicationContext app = new ClassPathXmlApplicationContext("ApplicationContext.xml");
        ComboPooledDataSource cpd = app.getBean("dataSource", ComboPooledDataSource.class);

        Connection connection = cpd.getConnection();
        System.out.println(connection);
        connection.close();
    }
}
```

## 使用 `jdbc.properties` 配置文件配置数据源参数

可以通过 `.properties` 配置文件来配置相关参数，减小项目耦合程度，方便参数修改

### 1. 创建 `jdbc.properties` 并设置参数

首先在 `resource` 文件夹下创建新文件，名称为 `jdbc.properties` 

然后打开这个文件，输入以下内容并保存文件，这些内容就是上文中设置数据源的各项参数：

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=public
```

其中等号左边的名称就是后面获取参数时要用到的 `key`

### 2. 在 `test.java` 中创建 `ResourceBundle` 对象并调用参数

使用 `getBundle(Stirng baseName)` 创建对象，其中 `baseName` 就是第一步中创建的配置文件相对于 `resource` 的路径，不要后缀名

创建对象后，使用 `getString(String key)` 方法获取参数

```java
import com.alibaba.druid.pool.DruidDataSource;
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.Test;

import java.sql.Connection;
import java.util.ResourceBundle;

public class JunitTest {

    @org.junit.Test
    // 测试C3P0数据源，参数通过配置文件加载
    public void test3() throws Exception {
        
        // 读取配置文件并获取参数
        ResourceBundle rb = ResourceBundle.getBundle("jdbc");
        String driver = rb.getString("jdbc.driver");
        String url = rb.getString("jdbc.url");
        String username = rb.getString("jdbc.username");
        String password = rb.getString("jdbc.password");
        
        ComboPooledDataSource cpd = new ComboPooledDataSource();    // 创建数据源对象
        cpd.setDriverClass(driver);                                 // 设置数据源驱动
        cpd.setJdbcUrl(url);                                        // 设置数据源地址
        cpd.setUser(username);                                      // 设置用户名
        cpd.setPassword(password);                                  // 设置密码
        Connection connection = cpd.getConnection();                // 建立连接
        System.out.println(connection);
        connection.close();                                         // 关闭连接
    }
}
```

## 在Spring容器中获取配置文件的参数

在基于上一节配置了 `jdbc.properties` 文件后，我们可以在Spring容器中获取参数，进一步降低项目耦合程度

### 1. 修改 `ApplicationContext.xml` 的根标签

在 xml 配置文件的根标签中，添加 `context` 相关属性：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

		<!-- 中间代码略 -->

</beans>
```

### 2. 加载 `jdbc.properties` 文件

使用 `property-placeholder` 标签加载配置文件：

```xml
<context:property-placeholder location="classpath:jdbc.properties"/>
```

其中的 `location` 属性指的是配置文件的位置，输入值中的 `classpath:` 指的是配置文件相对于 `resource` 目录的路径

### 3. 调用参数

通过 SpEL 表达式调用参数，格式为 `${key}` ：

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driver}"/>
    <property name="jdbcUrl" value="${jdbc.url}"/>
    <property name="user" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```

完整的 `ApplicationContext.xml` 如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

	<!-- 在此处加载jdbc.properties -->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!-- 在此处调用参数 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

</beans>
```

