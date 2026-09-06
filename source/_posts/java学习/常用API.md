---
title: Java-常用工具类
hidden: false
categories:
  - 学习记录 
  - Java开发
tags:
  - Java
abbrlink: e3dac448
date: 2026-04-29 22:41:27
---

## Math类

Math类是用于数学计算的类，不需要创建对象就可以调用类中的方法，如求绝对值、四舍五入、求某个数的n次幂等，以下为一些常见的Math方法示例：

```java
public class test1 {
    public static void main(String[] args) {
        System.out.println(Math.abs(-1));                               // 取绝对值
        System.out.println(Math.ceil(3.21));                            // 向上取整
        System.out.println(Math.floor(4.56));                           // 向下取整
        System.out.println(Math.round(5.51));                           // 四舍五入
        System.out.println(Math.max(3,5.4) + Math.min(3,5.4));          // 比较大小
        System.out.println(Math.pow(3.5,3));                            // a的b次幂
        System.out.println(Math.sqrt(4) + Math.cbrt(8));                // 开平方根与立方根
    }
}
```

Math类也定义了一些较高精度的数学常数，如圆周率pi和自然常数e等

```java
// Math.java
    public static final double E = 2.718281828459045;
    public static final double PI = 3.141592653589793;
    public static final double TAU = 2.0 * PI;
    private static final double DEGREES_TO_RADIANS = 0.017453292519943295;
    private static final double RADIANS_TO_DEGREES = 57.29577951308232;
```

## System类

System类是与系统相关的类，提供了操作系统相关的方法，不需要创建对象就可以调用类中的方法，以下是示例方法：

```java
import java.util.Arrays;

public class test1 {
    public static void main(String[] args) {
        System.out.println(System.currentTimeMillis());    // 返回当前时间与时间原点的差值（单位ms）

        int[] arr = {1,2,3,4,5,6};
        int[] arr1 = new int[3];
            // arraycopy(原数组, 原数组起始下标, 目标数组, 目标数组起始下标, 复制长度)
        System.arraycopy(arr, 3, arr1, 0, 3);   // 按要求复制数组
        System.out.println(Arrays.toString(arr));
        System.out.println(Arrays.toString(arr1));

        System.exit(0);    // 退出Java虚拟机并返回指定的exitCode
    }
}
```

其中**时间原点**为1970年1月1日0时0分0秒（UTC+0）

## Runtime类

Runtime类与程序运行的环境有关，也不需要创建对象就可以调用类中的方法，以下为一些常见的方法示例：

```java
import java.io.IOException;

public class test1 {
    public static void main(String[] args) throws IOException {
        Runtime rt = Runtime.getRuntime();              // 获取运行环境对象
        System.out.println(rt.availableProcessors());   // 获取cpu线程数
        System.out.println(rt.maxMemory());             // 查询JVM可以获取的最大内存（单位为Byte）
        System.out.println(rt.totalMemory());           // 查询JVM已经获取的内存大小（单位为Byte）
        System.out.println(rt.freeMemory());            // 查询JVM剩余内存大小（单位为Byte）
        rt.exec(new String[]{"notepad"});               // 运行cmd命令（这里为打开记事本）
        rt.exit(0);                                     // 退出虚拟机
    }
}
```

## Object类

Object类是Java中的最高父类，所有的类都直接或间接继承于Object类

Object类没有带参数的构造方法，只有空参构造方法，但是可以使用其子类的构造方法

```java
public static void main(String[] args) {
    Object obj = new Object();        // 使用空参构造
    Object obj1 = new int[4];        // 使用int[]的构造方法
}
```

以下为Object类中的常见成员方法，这些方法都可以被重写：

```java
public class test {
    public static void main(String[] args) {
        System.out.println(obj1.toString());        // 将对象转为字符串
        System.out.println(obj1.equals(obj2));      // 判断两个对象是否相等
    }
}
```

此外，Object类中还有一个 `clone()` 方法，用于克隆对象的属性值：

```java
// test1.java（需要实现 Cloneable 接口）
public class test1 implements Cloneable {
    int anInt;
    double aDouble;
    String aString;

    // 构造方法以及getter和setter

    // 重写clone()方法
    @Override
    protected Object clone() throw CloneNotSupportedException {
        return super.clone();
    }
}


// Main.java
public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        test1 t1 = new test1(1, 2.0, "3");
        test1 t2 = (test1) t1.clone();        // 调用clone()方法
        System.out.println(t1);
        System.out.println(t2);                // 这两个对象的输出内容是相同的
    }
}
```

这里需要介绍一下深克隆与浅克隆的区别，它们主要体现在对引用数据类型成员变量的复制（除了字符串类型）：

- 浅克隆：直接复制成员的地址。这表明浅克隆出来的对象B，与原对象A共用相同的引用数据类型成员变量
- 深克隆：先创建与成员变量X相同类型的新变量Y，并复制成员变量X的内容，然后再复制新变量Y的地址。这表明深克隆出来的对象B，与原对象A不共用引用数据类型的成员变量

`clone()` 方法默认为浅克隆，当然我们也可以通过重写方法来实现深克隆

## Objects类

Objects是一个工具类，提供了一些工具方法，不需要创建对象

Objects有三个常用的成员方法：

```java
import java.util.Objects;

public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        test1 t1 = new test1(1, 2.0, "3");
        test1 t2 = (test1) t1.clone();

        System.out.println(Objects.equals(t1, t2));        // 两个对象是否相等
        System.out.println(Objects.isNull(t1));            // 对象是否为null
        System.out.println(Objects.nonNull(t2));            // 对象是否不为null
    }
}
```

# 
