---
title: Java反射之私有变量和私有方法
date: 2016-05-14 00:52:55
tags:
    - Java
---
原文地址[Java Reflection Methods](http://tutorials.jenkov.com/java-reflection/private-fields-and-methods.html)

通常情况下，从对象外部访问私有变量和私有方法是不被允许的，但是通过反射可以很容易获取私有变量和私有方法，在单元测试的时候很有用。

###### Accessing Private Fields

获取私有变量你需要用到`Class.getDeclaredField(String name)`或者`Class.getDeclaredFields(String name)`方法。`Class.getField(String name)`和`Class.getFields(String name)`方法只能返回公有变量。下面是通过java的反射获取私有变量代码:

```java
        Order privateObject = new Order("2016051901");
        try {
            Field privateField = Order.class.getDeclaredField("orderId");
            privateField.setAccessible(true);
            String orderId = (String) privateField.get(privateObject);
            System.out.println("orderId:" + orderId);

        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
```

这段代码会打印出『orderId:2016051901』，值为Order实例私有变量orderId的值。

`Order.class.getDeclaredField("orderId")`这个方法返回一个私有变量，这个私有变量是Order类中自己定义的变量，而不是继承自其父类的变量。

`privateField.setAccessible(true);`这个方法会关闭实例类的反射访问检查。现在你可以访问私有的，受保护的和包级访问的变量。

###### Accessing Private Methods

要获取私有方法你需要使用方法`Class.getDeclaredMethod(String name, Class[] parameterTypes)`或者`Class.getDeclaredMethods()`。`Class.getMethod()`和`Class.getMethods()`方法只是返回的公有方法。下面是使用java反射访问私有方法的代码：

```java
        Order privateObject = new Order("2016051901");
        try {
            Method privateMethod = Order.class.getDeclaredMethod("getOrderId");
            privateMethod.setAccessible(true);
            String returnValue = (String) privateMethod.invoke(privateObject);
            System.out.println("returnValue = " + returnValue);
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
```

这段代码会打印出『returnValue=2016051901』，其值是通过反射调用Order实例的私有方法『getOrderId()』获取到的。

`Order.class.getDeclaredMethod("getOrderId")`,这个方法也之后返回Order类自己的私有方法，而非其继承自父类的私有方法。

`privateMethod.setAcessible(true)`，这个方法会关闭实例类的私有方法反射访问检查，现在你可以通过反射获取的实例的私有，受保护和包级访问权限的方法。 
