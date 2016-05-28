---
title: Java反射之构造函数
date: 2016-05-11 00:52:55
tags:
    - Java
---
原文地址[Java Reflection Constructors](http://tutorials.jenkov.com/java-reflection/constructors.html)

#### 获取构造函数的对象

```java
        Constructor[] constructors = orderClass.getConstructors();
```

可以通过构造函数的具体参数类型直接获取单独的构造函数，而不用获取全部的Constructors

```java
        try {
            Constructor constructor = orderClass.getConstructor(new Class[]{String.class});
            System.out.println(constructor.getName());//com.larry.bean.Order
        } catch (NoSuchMethodException e) {//没有找到匹配的构造函数会抛出NoSuchMethodException
            e.printStackTrace();
        }
```

#### 构造函数的参数类型

可以像下面这样获取构造函数的参数

```java
        Class[] parameterTypes = constructor.getParameterTypes();
        parameterTypes[0].getName();//java.lang.String
```

#### 用构造函数对象实例化一个类

constructor.newInstance()方法的方法参数是一个可变参数列表，但是当你调用构造方法的时候你必须提供精确的参数，即形参与实参必须一一对应。在这个例子中构造方法需要一个String类型的参数，那我们在调用newInstance方法的时候就必须传入一个String类型的参数

```java
        try {
            Constructor constructor = Order.class.getConstructor(String.class);
            Order order = (Order) constructor.newInstance("2016050901");
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
```


