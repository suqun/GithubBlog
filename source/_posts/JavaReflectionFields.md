---
title: Java反射之变量
date: 2016-05-12 00:52:55
tags:
    - Java
---
### Java Reflection Fields
原文地址[Java Reflection Fields](http://tutorials.jenkov.com/java-reflection/fields.html)

#### Obtaining Field Objects

```java
        Field[] fields = orderClass.getFields();
```

Field[]数组里面只包含类中public修饰的成员变量

如果知道类的成员变量名称，可以直接通过getField()方法获取到，成员变量的访问权限同样需要是public的

#### Field Name and Type

```java
        try {
            Field field = orderClass.getField("description");
            System.out.println(field.getName());//成员变量名称description
            System.out.println(field.getType());//成员变量类型class java.lang.String
        } catch (NoSuchFieldException e) {//如果不存在description的变量，会抛出NoSuchFieldException
            e.printStackTrace();
        }
```

#### Getting and Setting Field Values

获得了field的引用，就可以通过get()，set()方法，获取和设置成员变量的值

```java
        try {
            Field field = orderClass.getField("description");
            Order order = new Order();
            Object value = field.get(order);
            field.set(order, value);
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
```

传入Field.get()/Field.set()方法的参数order是Order类的实例。
如果变量是静态变量的话(public static)那么在调用Field.get()/Field.set()方法的时候传入null做为参数而不用传递拥有该变量的类的实例(传入类的实例也是可以的)。

