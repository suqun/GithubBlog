---
title: Java反射之注解
date: 2016-05-18 00:52:55
tags:
    - Java
---
原文地址[Java Reflection Annotations](http://tutorials.jenkov.com/java-reflection/annotations.html)

在运行时状态下，你可以通过反射获取java对象上的注解。

#### What are Java Annotations?

注解是Java5增加的功能。注解是一种注释或者是元数据可以直接插入到Java代码中。在编译时，通过预编译工具处理；或者在运行时，通过java反射处理。下面是个注解的例子：

```java
    @MyAnnotation(name="someName",  value = "Hello World")
    public class TheClass {

    }
```

类TheClass的上面有个`@MyAnnotation`的注解。注解的定义类似接口定义，下面是注解定义的例子：

```java
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.TYPE)

    public @interface MyAnnotation {
        public String name();
        public String value();
    }
```

创建注解是，在interface前面使用`@`即可。创建之后就可以在代码里使用了，例如上面的例子。

`@Retention(RetentionPolicy.RUNTIME)` 和 `@Target(ElementType.TYPE)`这两个指令，指明了这个注解如何被使用。
 
`@Retention(RetentionPolicy.RUNTIME)` 意味着在运行时状态可以使用java反射获取注解，如果不设置这个指令，在运行时状态，注解不会被保存，同样的也就不能通过反射获取。 

`@Target(ElementType.TYPE)`意味着注解只能用在Types上（比如类和接口）。你也可以指定为`METHOD`或者是`FIELD`，或者是不使用Target这个指令，这样你就可以在类，方法，变量上使用了。

#### Class Annotations

你可以在运行期访问类，方法，变量的注解。下面是类注解的例子：

```java
    Class aClass = TheClass.class;
    Annotation[] annotations = aClass.getAnnotations();

    for(Annotation annotation : annotations){
        if(annotation instanceof MyAnnotation){
            MyAnnotation myAnnotation = (MyAnnotation) annotation;
            System.out.println("name: " + myAnnotation.name());
            System.out.println("value: " + myAnnotation.value());
        }
    }
```

也可以指定类进行访问，例如：

```java
    Class aClass = TheClass.class;
    Annotation annotation = aClass.getAnnotation(MyAnnotation.class);

    if(annotation instanceof MyAnnotation){
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("name: " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
```

#### Method Annotations

下面是注解在方法上的例子：

```java
public class TheClass2 {
  @MyAnnotation(name="someName",  value = "Hello World")
  public void doSomething(){}
}
```

可以像下面这样访问方法注解：

```java
            Method method = TheClass2.class.getMethod("doSomething");
            Annotation[] annotations = method.getDeclaredAnnotations();
            for(Annotation annotation : annotations){
                if(annotation instanceof MyAnnotation){
                    MyAnnotation myAnnotation = (MyAnnotation) annotation;
                    System.out.println("name: " + myAnnotation.name());
                    System.out.println("value: " + myAnnotation.value());
                }
            }
```

或者是指定方法注解：

```java
            Method method = TheClass2.class.getMethod("doSomething");
            Annotation annotation = method.getAnnotation(MyAnnotation.class);
            if (annotation instanceof MyAnnotation) {
                MyAnnotation myAnnotation = (MyAnnotation) annotation;
                System.out.println("name:"+myAnnotation.name());
                System.out.println("value:"+myAnnotation.value());
            }
```

#### Parameter Annotations

在方法的参数上使用注解：

```java
public class TheClass3 {
  public static void doSomethingElse(
        @MyAnnotation(name="aName", value="aValue") String parameter){
  }
}
```

访问方法参数上的注解：

```java
Method method = TheClass3.class.getMethod("doSomethingElse");
Annotation[][] parameterAnnotations = method.getParameterAnnotations();
Class[] parameterTypes = method.getParameterTypes();

int i=0;
for(Annotation[] annotations : parameterAnnotations){
  Class parameterType = parameterTypes[i++];

  for(Annotation annotation : annotations){
    if(annotation instanceof MyAnnotation){
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("param: " + parameterType.getName());
        System.out.println("name : " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
  }
}
```

注意Method.getParameterAnnotations()返回的是二维数组，包含每个参数的注解数组。

#### Field Annotations 

变量上的注解使用：

```java
public class TheClass4 {

  @MyAnnotation(name="someName",  value = "Hello World")
  public String myField = null;
}
```

访问变量上的注解：

```java
Field field = TheClass4.class.getField("myField");
Annotation[] annotations = field.getDeclaredAnnotations();

for(Annotation annotation : annotations){
    if(annotation instanceof MyAnnotation){
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("name: " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
}
```

或者：

```java
Field field = TheClass4.class.getField("myField");
Annotation annotation = field.getAnnotation(MyAnnotation.class);

if(annotation instanceof MyAnnotation){
    MyAnnotation myAnnotation = (MyAnnotation) annotation;
    System.out.println("name: " + myAnnotation.name());
    System.out.println("value: " + myAnnotation.value());
}
```




