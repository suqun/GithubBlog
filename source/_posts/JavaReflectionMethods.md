---
title: Java反射之方法
date: 2016-05-13 00:52:55
tags:
    - Java
---
原文地址[Java Reflection Methods](http://tutorials.jenkov.com/java-reflection/methods.html)

##### Obtaining Method Objects

获取类的方法，Method[]数组里面只包含类中public修饰的方法

```java
        Method[] methods = orderClass.getMethods();
```

也可以使用具体的方法名及参数类型直接获取,以Order类中的getState()，setState()方法为例:

```java
        try {
            Method methodHaveNoParameter = orderClass.getMethod("getState",null);//该方法没有参数,null省略也可以
            System.out.println(methodHaveNoParameter.getName());//getState
            
            Method methodHaveParameter = orderClass.getMethod("setState",new Class[]{String.class});//该方有String类型参数
            System.out.println(methodHaveParameter.getName());//setState

        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        }
```

#### Method Parameters and Return Types

获取方法的参数类型及返回类型

```java
            Class[] parameters = methodHaveParameter.getParameterTypes();
            System.out.println(parameters[0].getName());//class java.lang.String

            Class returnType = methodHaveParameter.getReturnType();
            System.out.println(returnType);//void
```

#### Invoking Methods using Method Object

使用`invoke`调用方法,以Order类中的pay()方法为例，该方法直接返回一个字符串

```java
        try {
            Method methodPay = orderClass.getMethod("pay",new Class[]{String.class});
            Order order = new Order();
            Object returnValue = methodPay.invoke(order,"2016050902");
            System.out.println(returnValue);//2016050902 pay success
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
```

如果方法是static的，那么在调用invoke时，第一个参数（类的实例）可以传null(传入类的实例也是可以的)。

##### 获取getter/setter方法

使用反射时，如果需要获取类的getter和setter方法，需要获取所有methods，并根据getter/setter的特点找到他们。

- Getter
    getter方法以「get」开头，没有参数，有返回值
- Setter
    setter方法以「set」开头，有一个参数，返回值可能有可能没有

下面是从类中找到get/set方法的例子

```java
        public static void printGettersSetters(Class aClass){
          Method[] methods = aClass.getMethods();

          for(Method method : methods){
            if(isGetter(method)) System.out.println("getter: " + method);
            if(isSetter(method)) System.out.println("setter: " + method);
          }
        }

        public static boolean isGetter(Method method){
          if(!method.getName().startsWith("get"))      return false;
          if(method.getParameterTypes().length != 0)   return false;  
          if(void.class.equals(method.getReturnType()) return false;
          return true;
        }

        public static boolean isSetter(Method method){
          if(!method.getName().startsWith("set")) return false;
          if(method.getParameterTypes().length != 1) return false;
          return true;
        }
```
