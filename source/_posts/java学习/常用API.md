---
abbrlink: '0'
---
### 常用API

#### 1. Math类

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

#### 2. System类

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

#### 3. Runtime类

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

#### 4. Object和Objects类

Object类是Java中的最高父类，所有的类都直接或间接继承于Object类
