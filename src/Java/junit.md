Title: Java Web 的测试
tags: Java, JUnit
date: 2016-1-23

前几天研究了 Java Web 中如何利用 log4j 记录 log，方便追踪少见的错误和异常，更加优雅的拜托 stdout 来 debug。这几天主要要钻研一下如何在一个 Java Web 的大型系统中优雅的做测试，包括单元测试和集成测试。

由于工程庞大，错综复杂，而且目前一直处于在线调试的状态，缺少各种基础设施，所以加入测试难度颇高，今天简单的搜索了一下，检索到了一些关键技术和工具，暂作记录，空下来后慢慢研究。

1. JUnit 4 -- StrutsSpringJUnit4TestCase
2. [Watji](http://watij.com/)
3. h2 in-memory database
4. Mock Class

感觉想要成功的为整套系统加入测试，对于很多 Java 的概念和框架还需要深入学习，目前看来，遇到 IoR 和 Bean 相关的概念时还是有些不明所以。

想要进行单元测试，有几个问题需要解决

1. 如何模拟 Request 请求
2. 如何判断请求处理是否正常
3. 如何劫持 Hibernate 的数据库操作

关于第一三两个问题，StackOverflow 上很多答案都提到了 Mock Class 的概念，似乎是利用了 IoR 区分了开发和部署环境下 Hibernate 的行为。为了更好的理解这些用法，还需要对 Spring Core 进行深入的研究。

说白了 SSH 经典框架我现在就研究了 Struts2，还总被坑奇怪的地方坑。

Watji 是一个通过模拟浏览器行为，加入断言库来进行测试的框架，优点在于可以测试页面在浏览器中的行为，可能还可以处理 JavaScript 页面的展示问题。问题在于，现在的系统大量使用了 iframe 的嵌套，但是我在 API 中没有看到对于 iframe 如何进行分析。

其次还需要把整个工程的几个G代码全都拿来梳理/整理一遍，重新设计工程结构，方便本地开发、调试、测试和发布，这么看来，还应该学习一下 Gradle 的用法。

简单的记录了一下这几天的学习，都是一些技术性上的问题，但是在处理 legacy code 的问题上，还需要学习一些哲学上和概念上的思路。买了本《重构》已经在路上了，任重而道远。

总之，Java 最擅长创造名词……
