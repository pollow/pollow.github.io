title: Gradle in Action 阅读笔记
date: 2016-02-09

最近发现各种技术书籍真是啰里啰嗦絮絮叨叨，就不能平铺直叙说清楚事情么……

## 简洁

Gradle的优点在于其保留了Ant、Maven的可扩展性，一定程度的向上兼容，方便历史项目缓慢的向Gradle迁移的同时，还提供更加简洁语法和底层的api，并且使用了Ivy和Maven的包仓库。**这种借用流行的当前项目资源，提供向上兼容的同时提供更加优良的特性的开源项目开发方法，值得借鉴**。和Maven一样，Gradle不是简单的Make程序或者npm的包管理器，而是一整套的构建工具(building tools)，当然，对于个人小项目，使用它的主要目的也仅仅是make好依赖管理以及自动测试，对于团队来说，更好的分离了测试环境和产品部署，简化了持续交付。

## 入门

### 构建

Java项目的构建和部署本身十分繁杂，而且大多数情况下都仅仅使用默认配置(convention)。Gradle将这些convention总结起来，利用其插件机制作为了一个Java项目插件，只要在构建脚本（默认为`build.gradle`，也可以在命令行中通过参数指定脚本）中使用此插件，即可包含所有的默认配置。所以，写一个简单的项目，并且在根目录新建包含如下内容的`build.gradle`脚本：

```gradle
apply plugin: 'java'
```

在命令行中使用`gradle build`命令即可完成构建。

显然，默认配置不能满足每个人自己的项目需求，我们需要自定义。只要在脚本后面加上属性值，即可覆盖默认设置：

```gradle
apply plugin: 'java'
version = 0.1
sourceCompatibility = 1.8

jar {
	manifest {
		attributes 'Main-Class': 'me.aubee.todo.ToDoApp'
	}
}
```

这个脚本设置了当前应用的版本号（体现在jar包上），兼容的JRE环境，以及Manifest文件的启动路径（要打包成可执行的jar包(standalone)，还可以使用`application plugin`，参见[这个链接](https://docs.gradle.org/current/userguide/application_plugin.html)）。

所有可用的属性（properties）可以通过命令行`gradle properties`查看。

### 依赖

由于之前最流行的依赖管理工具是Maven，所有Gradle支持使用Maven的软件仓库：

```gradle
repositories {
	mavenCentral()
}

dependencies {
	compile group: 'org.apache.commons', name: 'commons-lang3', version: '3.1'
}	
```

group, name, version 分别对应了Maven的groupId, artifactId和version。如上的依赖也可以写成简写

```gradle
dependencies {
	compile 'org.apache.commons:commons-lang3:3.1'
}	
```

这样，build的时候就会自动从Maven仓库中下载对应的jar包。

### Web Application

使用`war plugin`，默认需要将web resource放置到`src/main/webapp`中，构建中会多出war环节。所有的webapp目录下的文件会被拷贝到WAR file的根下，编译的classes文件放置在`WEB-INF/classes`下，所有的依赖放置在`WEB-INF/lib`中，成为一个符合Java EE标准的war file，link到tomcat中即可使用。

还可以使用`jetty plugin`，利用Gradle内嵌的一个jetty服务器运行webapp，但是由于过于legacy，暂时不作考虑。在构建的时候加一个soft link到tomcat的目录即可。

### Gradle Wrapper

这是一个用来消除多人合作时，由于跨平台和不同版本Gradle引起的不兼容型问题的脚本工具。使用很简单，暂时也不需要，不多讨论。

## 基础

