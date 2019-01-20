---
title: 枚举根据状态获取不同属性及在Android开发中优先使用注解，慎用枚举
date: 2018-07-07 13:22:09
categories:
  - Java
tags:
  - Android
  - Java
---


```java
public enum Status {
    SUCCESS(1,"成功"),
    FAIL(2,"失败");
    int mCode;
    String mText;

    Status(int i, String s) {
        mCode=i;
        mText=s;
    }
    public static Status getStatus(int i){
        for (Status status:values()){
            if (i==status.mCode){
                return status;
            }
        }
        return FAIL;
    }
}
```

调用：

```java
    public static void main(String[] args) {
        for (Status status:Status.values()){
            System.out.println(status.mText);
        }
    }
```

输出：

```shell
成功
失败

Process finished with exit code 0
```
#### 优先使用注解，慎用枚举

1. 原因

> 与静态常量相比，枚举会增大应用程序编译后的 dex 文件，同时应用在运行时的内存占用也会升高。在资源有限的移动设备上，大量的使用枚举无疑是致命的。

2. 寻找替代方案

   * 使用常量替换枚举

     ```java
     public class StatusConstants {
         public static final int SUCCESS = 1;
         public static final int FAIL = 0;
     }
     ```
     ```java
     public static void main(String[] args) {
             doSth(StatusConstants.SUCCESS);
             doSth(StatusConstants.FAIL);
         }
         public static void doSth(int status){
             switch (status){
                 case StatusConstants.SUCCESS:
                     System.out.println("成功");
                     break;
                 case StatusConstants.FAIL:
                     System.out.println("失败");
                     break;
             }
         }
     ```

               上述代码达到了和枚举一样的效果，完全可以不使用枚举。可是，让我再回到使用枚举的好处：

   * 保证了类型安全：调用者无法随意传一个 int 值；

   * 代码可读性非常高；
          反过来对比看，向上面那样使用常量会有什么问题:

   * 首先，我们无法保障类型安全，用户可以在调用时传入任何一个 int 值：

     ```java
     doSth(50);
     ```

   * 其次代码可读性很差，IDE 只是提示传入 int 类型的参数。此时如果不看方法体，调用者根本不知道该传什么值。

3. 接下来就要使用注解来解决这个问题了。

   * 引入注解库

   ```groovy
   compile 'com.android.support:support-annotations:25.0.0'
   ```

   * 建立状态注解

   ```java
   @Retention(RetentionPolicy.SOURCE)
   @Target(ElementType.PARAMETER)
   @IntDef({Status.SUCCESS, Status.FAIL})
   public @interface Status {
       int SUCCESS = 1;
       int FAIL = 0;
   }
   ```

   * 调用函数

   ```java
   public class StatusHelper {
       public static void doSth(@Status int status) {
           switch (status) {
               case Status.SUCCESS:
                   System.out.println("成功");
                   break;
               case Status.FAIL:
                   System.out.println("失败");
                   break;
           }
       }
   }
   ```

   * 测试：

   ```java
   public class Main {
       public static void main(String[] args) {
           doSth(Status.SUCCESS);
           doSth(Status.FAIL);
           doSth(155);//这里会显示红色错误
       }
   }
   ```

   虽然显示红色错误，但仍可以执行，不过这样已经起到警告的作用了。