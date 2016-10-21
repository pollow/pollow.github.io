title: Java Language Notes

Date:2016-03-05



虽然没有长期使用Java的打算，但是本着深入学习语言和设计的想法，还是决定多了解一些Java的高级用法。这几天计划着阅读 GSON 的代码，遇到了一些之前很少遇到的定义问题，简单记录和翻译一下官方文档，以便后人。

## Inner Class

Java 允许在一个 Class 的内部声明另外一个 Class，这个特性被称为 nested class，如下：

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

被声明为 static 的内部类被叫做 static nested classes, 而 non-static 的就被叫做 inner class。

所有的 Nested Class 都是外部类的成员，都可以访问到 enclosing class 的成员变量和方法。

Nested Class 的可见性可以是 private, public, protected 或者 package private，含义明显，不赘述。

**在编译成.class文件的时候，所有的内部类都会被编译成单独的.class文件，末尾附带有$\d+的标志。**

### Static Nested Class

Static Nested Class 的特殊在于只能够访问 enclosing class 的静态方法和静态变量，同时可以被静态的使用(实例化)，并且可以拥有非静态的成员变量和方法。

### Inner Class

Inner Class 总是伴随着一个实例化的 Enclosing Class，并且可以直接访问所有的成员和方法，并且由于它总是伴随着 Enclosing Class，所以不能够定义 static 成员在其中。因为 Inner Class 有 Enclosing Class 的引用，所以想要实例化一个 Inner Class, 必须首先实例化 Enclosing Class, 如下：

```java
class OuterClass {
    ...
    class InnerClass {
        ...
    }
}

OuterClass.InnerClass innerObject = outerObject.new InnerClass();
```

我之所以对 Inner Class 产生疑问，就是因为在阅读 GSON 的 [User Guide ](https://github.com/google/gson/blob/master/UserGuide.md#TOC-Overview) 的时候看到，不支持直接实例化一个 Inner Class，从而感觉自己对 Inner Class 的语法和规范有所困惑。

### Local Class

Local Class 是定义在 Block 中的 Class, 在写UI(或者是频繁实例化 interface)的时候经常用到，值得注意的是 Local Class 只能访问 enclosing local scope 中的变量(Java 8 之前要求标志为final, **Java 8 之后允许effectively final**)。

Local Class 可以访问 Enclosing Class 的所有成员，但是注意，**定义在 static 函数中的 Local Class 只能够访问 static 变量**。Local Class 是 non-static Inner Class，因为他们都拥有对于 Enclosing Block 的访问权，所以 Local Class 和 Inner Class 类似，**都不能够定义 static 成员**。因此，也不能够在 Block 中定义 interface，因为 interface 本身是静态的。不过例外是，可以定义CONSTANT。

### Anonymous Class

匿名类和 Local Class 完全一致，除了没有名字。支持匿名类完全是为了代码的简洁性。借助匿名类可以在声明 Local  Class 的同时直接实例化。实际使用中，**主要用作实现 interface**。

## Genetic Type

