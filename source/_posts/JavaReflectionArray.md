---
title: Java反射之数组
date: 2016-06-24 20:52:55
tags:
    - Java
toc: true
---
原文地址[Java Reflection Arrays](http://tutorials.jenkov.com/java-reflection/arrays.html)

在Java反射里面处理数组有时是比较棘手的.特别是你需要获得数组对象的实际类型.例如 int[]等待.这篇文字就来讨论通过反射如何创建数组如何获取数组中的对象.

#### java.lang.reflect.Array

通过Java反射使用数组用到的是类java.lang.reflect.Array,不要和Java集合中的java.util.Arrays混淆.

#### Creating Arrays

通过Java反射创建数组使用类java.lang.reflect.Array,下面给出了创建数组的例子:

```java
    int[] intArray = (int[]) Array.newInstance(int.class,3)
    
```

这是创建int类型数组的例子. Array.newInstance()方法的第一个参数`int.class`给出了数组元素类型,第二个参数`3`是数组需要分配的空间

#### Accessing Arrays

通过反射访问数组元素可以使用`Array.get()`和`Array.set()`方法:

```java
    int[] intArray = (int[]) Array.newInstance(int.class,3);
    Array.set(intArray,0,123)
    Array.set(intArray,1,456)
    Array.set(intArray,1,789)
   
    System.out.println("intArray[0] = " + Array.get(intArray, 0));
    System.out.println("intArray[1] = " + Array.get(intArray, 1));
    System.out.println("intArray[2] = " + Array.get(intArray, 2));
```

打印出来的结果为:

```java
    intArray[0] = 123
    intArray[1] = 456
    intArray[2] = 789
```

#### 获取数组的Class对象

在我编写[Butterfly DI Container](http://butterfly.jenkov.com/)的脚本语言时，当我想通过反射获取数组的Class对象时遇到了一点麻烦。如果不通过反射的话你可以这样来获取数组的Class对象：

```java
    Class stringArrayClass = String[].class;
```

如果使用Class.forName()方法来获取Class对象则不是那么简单。比如你可以像这样来获得一个原生数据类型（primitive）int数组的Class对象：

```java
    Class intArray = Class.forName("[I");
```
在JVM中字母I代表int类型，左边的‘[’代表我想要的是一个int类型的数组，这个规则同样适用于其他的原生数据类型。对于普通对象类型的数组有一点细微的不同：

```java
    Class stringArrayClass = Class.forName("[Ljava.lang.String;");
```

注意‘[L’的右边是类名，类名的右边是一个‘;’符号。这个的含义是一个指定类型的数组。需要注意的是，你不能通过Class.forName()方法获取一个原生数据类型的Class对象。下面这两个例子都会报ClassNotFoundException：

```java
    Class intClass1 = Class.forName("I");
    Class intClass2 = Class.forName("int");
```

我通常会用下面这个方法来获取普通对象以及原生对象的Class对象：

```java
public Class getClass(String className){
  if("int" .equals(className)) return int .class;
  if("long".equals(className)) return long.class;
  ...
  return Class.forName(className);
}
```

一旦你获取了类型的Class对象，你就有办法轻松的获取到它的数组的Class对象，你可以通过指定的类型创建一个空的数组，然后通过这个空的数组来获取数组的Class对象。这样做有点讨巧，不过很有效。如下例：

```java
    Class theClass = getClass(theClassName);
    Class stringArrayClass = Array.newInstance(theClass, 0).getClass();
```
这是一个特别的方式来获取指定类型的指定数组的Class对象。无需使用类名或其他方式来获取这个Class对象。
为了确保Class对象是不是代表一个数组，你可以使用Class.isArray()方法来进行校验：

```java
Class stringArrayClass = Array.newInstance(String.class, 0).getClass();
System.out.println("is array: " + stringArrayClass.isArray());
```

#### 获取数组的成员类型

一旦你获取了一个数组的Class对象，你就可以通过Class.getComponentType()方法获取这个数组的成员类型。成员类型就是数组存储的数据类型。例如，数组int[]的成员类型就是一个Class对象int.class。String[]的成员类型就是java.lang.String类的Class对象。
下面是一个访问数组成员类型的例子：

```java
String[] strings = new String[3];
Class stringArrayClass = strings.getClass();
Class stringArrayComponentType = stringArrayClass.getComponentType();
System.out.println(stringArrayComponentType);
```
下面这个例子会打印`java.lang.String`代表这个数组的成员类型是字符串。