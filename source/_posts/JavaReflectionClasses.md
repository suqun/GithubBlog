---
title: Java反射之对象
date: 2016-05-09 00:52:55
tags:
    - Java
---
原文地址[Java Reflection Classes](http://tutorials.jenkov.com/java-reflection/classes.html)
    
##### 获取class对象
Java中的所有类型包括基本类型(int, long, float等等)，即使是数组都有与之关联的Class类的对象。
如果你在编译期知道一个类的名字的话，那么你可以使用如下的方式获取一个类的Class对象

```java
        Class orderClass = Order.class;
```

如果你在编译期不知道类的名字，但是你可以在运行期获得到类名的字符串,那么你则可以这么做来获取Class对象

```java
        String className = ... ;//在运行期获取的类名字符串
        Class class = Class.forName(className);
```

在使用Class.forName()方法时，你必须提供一个类的全名，这个全名包括类所在的包的名字

##### Class Name 类名

通过`getName()` 方法返回类的全限定类名,不包含包名使用`getSimpleName()`

```java
        orderClass.getName();//com.larry.bean.Order
        orderClass.getSimpleName();
```

##### Modifiers 修饰符

通过Class对象来访问一个类的修饰符，即public,private,static等的关键字;

修饰符都被包装成一个int类型的数字，这样每个修饰符都是一个位标识(flag bit)，这个位标识可以设置和清除修饰符的类型;

使用java.lang.reflect.Modifier类中的方法来检查修饰符的类型。

```java
        int modifiers = orderClass.getModifiers();

        Modifier.isAbstract(modifiers);
        Modifier.isFinal(modifiers);
        Modifier.isInterface(modifiers);
        Modifier.isNative(modifiers);
        Modifier.isPrivate(modifiers);
        Modifier.isProtected(modifiers);
        Modifier.isPublic(modifiers);//true
        Modifier.isStatic(modifiers);
        Modifier.isStrict(modifiers);
        Modifier.isSynchronized(modifiers);
        Modifier.isTransient(modifiers);
        Modifier.isVolatile(modifiers);
```

##### Package Info 包

Package对象你可以获取包的相关信息，比如包名

```java
        Package packages = orderClass.getPackage();
        packages.getName();//com.larry.bean
```

##### Superclass 父类

superclass对象其实就是一个Class类的实例，所以你可以继续在这个对象上进行反射操作

```java
        Class superClass = orderClass.getSuperclass();
        superClass.getName();//java.lang.Object
```

##### Implemented Interfaces 实现的接口

获取指定类所实现的接口集合

```java
        Class[] interfaces = orderClass.getInterfaces();
```

getInterfaces()方法仅仅只返回当前类所实现的接口。当前类的父类如果实现了接口，这些接口是不会在返回的Class集合中的，尽管实际上当前类其实已经实现了父类接口

##### Constructors 构造函数

###### 获取构造函数的对象

```java
        Constructor[] constructors = orderClass.getConstructors();
```

##### Method 方法

获取类的方法，Method[]数组里面只包含类中public修饰的方法

```java
        Method[] methods = orderClass.getMethods();
```

##### Field 变量

获取成员变量

```java
        Field[] fields = orderClass.getFields();
```

##### Annotations 注解

获取类的注解

```java
Annotation[] annotations = orderClass.getAnnotations();
```


