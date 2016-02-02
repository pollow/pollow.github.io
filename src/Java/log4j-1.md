Title: Log4j 学习与配置
tags: Java, log4j
date: 2016-1-22

开始的时候就被这个项目居然使用 System.out.println 调试大法震惊了一下，由于我也懒得在架构上动手脚，所以也就随大流将就一下。但是最近遇到几个问题，一是我写的一个方法逻辑太过复杂，有太多的数据库操作（其实应该避免，但是因为API不够丰富，逻辑又十分复杂暂时只能如此了）；二是有一些客户报告了一下我在本地无法复现，有很偶然才会发生的 bug，不得已，我决定使用一下 Logger，在相关的函数中记录一下关键值。


## 基础使用

记录需要使用 Logger, Logger 应该是单例模型，在应用初始化的时候创建并且保留以后一直使用；不过 LogManager 会处理这些事情，只要提供合适的 Logger 名称，它会在内部管理所有的 Logger。Logger的名字推荐和使用的类名一致，这也是`LogManager.getLogger() `的默认行为。

常用的又 INFO, DEBUG, WARN, ERROR 四个等级，对应四个函数；可以使用 printf 函数默认调用 String Formatter；有 entry, exit 两个函数处理 Flow Tracing。（如果每个函数都要在进入和退出的时候调用这两个函数，是不是可以使用装饰器方法操作？）

```java
logger.debug("Logging in user {} with birthday {}", user.getName(), user.getBirthdayCalendar());
```

API 2.x 中表示可以直接调用函数，不需要检查 log 等级是否开启，不知道 API 1.x 中是否可以这样使用。

如果是 Java 8，可以借助 Lambda 表达式完成 lazy-evaluation.

```java
logger.trace("Some long-running operation returned {}", () -> expensiveOperation());
```

高级用法暂时不提。

## Configuration

感觉 log4j 的配置十分复杂，和各种框架也有耦合（主要是框架使用了 log4j 作为日志输出）。

Log4j 推荐的配置方法是通过配置文件进行配置，如果无法定位到配置文件的话，则会使用默认配置 DefaultConfiguration class. 默认输出到控制台，只显示 ERROR 级别。

Log4j 支持多种配置格式和文件名，具体可以参考 [Configuration](http://logging.apache.org/log4j/2.x/manual/configuration.html)。通常来说，在工程的配置目录中添加 `log4j2.properties` 文件最为合适。

一个 XML 设置的结构通常如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>;
<Configuration>
  <Properties>
    <Property name="name1">value</property>
    <Property name="name2" value="value2"/>
  </Properties>
  <filter  ... />
  <Appenders>
    <appender ... >
      <filter  ... />
    </appender>
    ...
  </Appenders>
  <Loggers>
    <Logger name="name1">
      <filter  ... />
    </Logger>
    ...
    <Root level="level">
      <AppenderRef ref="name"/>
    </Root>
  </Loggers>
</Configuration>
```

\<Configuration\> 可以包含 status 属性，配置 log4j 内部的 log 级别，遇到问题是可以设置为 trace 来排查。

**最关键的是**要将 log 写入文件中，需要设置 Appender 的类型。我声明 Appender

```xml
<Appender type="File">
```

运行时会出现 CLASS_NOT_FOUND 的异常。简单查询了一下，和 ClassLoader 有关，暂时没有研究；设置 Appender为

```xml
<File name="File" fileName="${filename}">
    <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
</File>
```

成功将 log 写入文件中。

## RollingFile

详细而复杂的 Appender 配置可以参考 [Appenders](http://logging.apache.org/log4j/2.x/manual/appenders.html#RollingFileAppender)。这里简单记录一下如何配置 RollingFile，既设置条件自动归档旧记录的方法。

配置`RollingFileAppender`需要设置`TriggeringPolicy`和`RolloverStrategy`两个属性，分别用来判断何时归档以及归档名称。

使用`Policies`标签设置`CompositeTriggeringPolicy`属性，内部可以包含多个`Policies`，任意一个为真都会产生新文件并归档旧文件。

使用`DefaultRolloverStrategy`配置命名策略，使用滑动窗口侧策略，有四个参数可以配置：fileIndex, min, max, compressionLevel。fileIndex可以设置为`min`或者`max`，表示新归档的日志的明明方向（最大 or 最小），max 为窗口大小，超过 max 后会自动删除最老的归档。同时，默认策略在`filePattern`中同时接受时间和序号两个参数。一下是官网提供的一个样例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="warn" name="MyApp" packages="">
  <Appenders>
    <RollingFile name="RollingFile" fileName="logs/app.log"
                 filePattern="logs/$${date:yyyy-MM}/app-%d{MM-dd-yyyy}-%i.log.gz">
      <PatternLayout>
        <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
      </PatternLayout>
      <Policies>
        <TimeBasedTriggeringPolicy />
        <SizeBasedTriggeringPolicy size="250 MB"/>
      </Policies>
    </RollingFile>
  </Appenders>
  <Loggers>
    <Root level="error">
      <AppenderRef ref="RollingFile"/>
    </Root>
  </Loggers>
</Configuration>
```

可以看出`%i`和`%d`分别代表了序号和时间，时间格式和Java常用的配置相同。这个配置默认会产生最多七个归档，但是代码中并没有7这个数字出现，猜测为默认配置。同时还会调用`gzip`压缩，猜测是通过文件后缀名判断的压缩算法……

以上介绍了简单的 RollingFile 配制方法，已经足够简单实用，更加复杂的需求请参考[官档](http://logging.apache.org/log4j/2.x/manual/appenders.html#RollingFileAppender)。

## Logger

在`Loggers`标签中可以指定多个`Logger`记录器。其中`Root`标签必须存在（在 Properties 文件中，使用`rootLogger`），作为根记录器，匹配所有的 log 行为。其他的`Logger`标签可以通过`name`属性指定匹配的`Logger`名称，也就是`LogManager.getLogger(name)`中传入的名称，默认为类名。

日志打印行为会匹配所有匹配的`Logger`标签，如果希望匹配后截断，可以指定`additivity`属性为false。

一个样例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
    <Properties>
        <Property name="filename">target/test.log</Property>
    </Properties>
    <Appenders>
        <Console name="Console">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>

        </Console>
    </Appenders>
    <Loggers>
        <Root level="trace">
            <AppenderRef ref="Console"/>
        </Root>
        <Logger name="com.trendcom.tour.A" level="trace">
            <AppenderRef ref="Console"/>
        </Logger>
        <Logger name="com.trendcom.tour" level="trace" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>
    </Loggers>
</Configuration>
```

## Log4j 1.x & 2.x

具体介绍请看 [Introduction](http://logging.apache.org/log4j/2.x/manual/index.html)，我浏览了一下系统依赖库，发现已经存在 Log4j 1.x，并且在根目录中也有 log4j.property。为了绕过系统级的迁移问题，如果不能并存，最好暂时使用 1.x。
