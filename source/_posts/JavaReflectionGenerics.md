---
title: Java反射之泛型
date: 2016-05-21 00:52:55
tags:
    - Java
---
原文地址[Java Reflection Generics](http://tutorials.jenkov.com/java-reflection/generics.html)

#### Generic Method Return Types

如果你已经获取一个`java.lang.reflect.Method`的对象，就可以获取到该对象上的泛型返回类型信息。如果方法是在一个被参数化类型之中（如T fun()）那么你无法获取他的具体类型，但是如果方法返回一个泛型类（如List fun()）那么你就可以获得这个泛型类的具体参数化类型。下面这个例子定义了一个类这个类中的方法返回类型是一个泛型类型：

```java
    public class MyClass {

        protected List<String> stringList = new ArrayList<String>();

        public List<String> getStringList() {
            return stringList;
        }
    }
```

这个例子是可以获取到`getStringList()`方法的泛型返回类型。可以检测到`getStringList()`方法返回的List并不仅仅是一个List。

```java
     try {
            Method method = MyClass.class.getMethod("getStringList");
            Type returnType = method.getGenericReturnType();
            if (returnType instanceof ParameterizedType) {
                ParameterizedType type = (ParameterizedType) returnType;
                Type[] typeArguments = type.getActualTypeArguments();
                for (Type typeArgument : typeArguments) {
                    Class typeArgClass = (Class) typeArgument;
                    System.out.println("typeArgClass:" + typeArgClass);//typeArgClass:class java.lang.String
                }
            }
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        }
```

Type[]数组typeArguments只有一个结果 – 一个代表java.lang.String的Class类的实例。Class类实现了Type接口。

#### Generic Method Parameter Types

You can also access the generic types of parameter types at runtime via Java Reflection. Here is an example class with a method taking a parameterized List as parameter:

```java
    public class MyClass {

        protected List<String> stringList = new ArrayList<String>();

        public void setStringList(List<String> stringList) {
            this.stringList = stringList;
        }
    }
```

You can access the generic parameter types of the method parameters like this:

```java
        Method method2 = MyClass.class.getMethod("setStringList", List.class);
        Type[] genericParameterTypes = method2.getGenericParameterTypes();
        for (Type genericParameterType : genericParameterTypes) {
            if(genericParameterType instanceof ParameterizedType){
                ParameterizedType aType = (ParameterizedType) genericParameterType;
                Type[] parameterArgTypes = aType.getActualTypeArguments();
                for(Type parameterArgType : parameterArgTypes){
                    Class parameterArgClass = (Class) parameterArgType;
                    System.out.println("parameterArgClass = " + parameterArgClass);//parameterArgClass = class java.lang.String
                }
            }
        }
```

This code will print out the text "parameterArgType = java.lang.String". The Type[] array parameterArgTypes array will contain one item - a Class instance representing the class java.lang.String. Class implements the Type interface.

#### Generic Field Types

It is also possible to access the generic types of `public` fields. Fields are class member variables - either static or instance variables. 

```java
    public class MyClass {
        protected List<String> stringList = new ArrayList<String>();
    }
```

```java
    Field field = MyClass.class.getField("stringList");

    Type genericFieldType = field.getGenericType();

    if(genericFieldType instanceof ParameterizedType){
        ParameterizedType aType = (ParameterizedType) genericFieldType;
        Type[] fieldArgTypes = aType.getActualTypeArguments();
        for(Type fieldArgType : fieldArgTypes){
            Class fieldArgClass = (Class) fieldArgType;
            System.out.println("fieldArgClass = " + fieldArgClass);
        }
    }
```

This code will print out the text "fieldArgClass = java.lang.String". The Type[] array fieldArgTypes array will contain one item - a Class instance representing the class java.lang.String. Class implements the Type interface.



