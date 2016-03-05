title: Java Language Notes

Date:2016-03-05



虽然没有长期使用Java的打算，但是本着深入学习语言和设计的想法，还是决定多了解一些Java的高级用法。这几天计划着阅读 GSON 的代码，遇到了一些之前很少遇到的定义问题，简单记录和翻译一下官方文档，以便后人。

## Inner Class

Java 允许在一个 Class 的内部声明另外一个一个 Class，这个特性被称为 nested class，如下：

```java
class OuterClass {
    ...
    class NestedClass {
        ...
    }
}
```

Nested Class 有 static 和 non-static 的区别，static 的声明如下：

```java
class OuterClass {
    ...
    static class StaticNestedClass {
        ...
    }
    class InnerClass {
        ...
    }
}
```

被声明为 static 的内部类被叫做 nested classes, 而 non-static 的就被叫做 inner class。

