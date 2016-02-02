title: Android 学习日记 Activity
date: 2016-02-02
tags: memo, android

文中出现的『书中』字样，均指代『《第一行代码》2014年第一版』。

我最终使用的是 Android 5.0 SDK 23，运行的虚拟机却是 Android 6.0；暂时未出现明显兼容性问题。

## Activity

一个 Activity（活动） 是 Android SDK 中 Activity 类的子类，负责呈现视图和数据，与用户进行交互。每一个活动需要一个对应的布局（layout）负责描述界面对象信息。

创建一个活动首先需要在包中创建一个新类，继承 Activity 类，重载`onCreate`函数，并在`onCreate`函数中调用`setContentView`来为活动家在布局。索引布局可以使用`R.layout.layout_id`；R文件在gen文件夹中，是自动生成的一个包含了所有资源id的java文件。

所有的活动都需要在`AndroidManifest.xml`中注册；在manifest标签中制定了package的话，可以省略Activity标签中class的包名。

在<activity>标签中指定的label作为标题栏内容（如何去除标题栏还有一些疑问），如果是主活动的话，这个也会作为应用名称出现在 Launcher(drawer) 中。如果没有在<activity>标签中指定label的话并且需要label的话，则会使用上一级的<application>的label属性。

一个<activity>标签中通常定义一些<intent-filter>标签，里面包含对于intent的定义（action & category），这个用于指定启动条件，后文再详述。一个**主活动**就是包含了如下<intent-filter>的活动：

```xml
<intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
```

这个活动就是打开应用的第一个活动，如果没有指定主活动，则无法在 Launcher 中看到应用，**如果指定多个活动**，则在Launcher中会出现多个对应的程序图标，分别指向各自的活动。

### 隐藏标题栏

书中『隐藏标题栏』一章中的方法在 Android 5.0 SDK 中已经不可用。

### Toast

Toast 就是在屏幕下方出现的半透明提醒框，一定时间后消失，没有其余的交互信息。推荐通过 Toast 将一些短小的消息通知给用户。

使用时，通过调用 Toast 的静态方法`makeText`创建一个 Toast 对象，调用这个对象的`show`方法展示。`makeText`需要三个参数：

1. Context
2. 文本内容（String）
3. 时长，通过两个常亮设定：`Toast.LENGTH_SHORT` & `Toast.LENGTH_LONG`

### Menu

书中的创建 Menu 方法已不可用；我创建的虚拟机基于 Nexus 4，已经没有了实体按键，Android 6.0 的虚拟按键中也没有菜单键。

### 销毁活动

按下 Back 键即可销毁活动并且返回上一个活动；在函数中调用`finish`方法也可以销毁活动。`finish`方法定义在`Activity`类中。

## Intent

当应用拥有多个活动时（通常情况），可以使用 Intent 进行切换，同时传递数据。

Intent 是 Android 程序中在各个组件之间切换的重要方式，本节最后将简要归纳所有的 Intent 用法，不局限在 Activity 组件当中。

### 显式调用

```java
Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
startActivityForResult(intent, 1);
```

直接调用 Intent 的构造函数，传入 Context 和目标 Activity 的 Class（内部使用了RTTI么？）；之后调用`startActivity`启动新的活动；之所以将这种调用方式称为**显式调用**，是因为在构造函数中显式制定了目标活动，相对应的，隐式调用就没有指明目标活动。

### 隐式调用

```java
Intent intent = new Intent("me.aubee.activitytest.ACTION_START");
startActivity(intent);
```

在调用 Intent 的构造函数的时候传入一个"action name"，再调用`startActivity`即可。如果没有指定category的话，默认使用`android.intent.category.DEFAULT`。这时会启动同时匹配action name和category name的Activity（在<intent-filter>标签中设置）。

如果有多个活动被匹配到，则会弹出选择框，选择启动哪个活动。同时，这也是和其他应用协同工作的机制，通过设置指定的action name，可以启动其他同时绑定了这个action name的应用程序。这里有两个额外的概念：Uri和Scheme。

Uri就是一个资源修饰符（Universal resource identifier），其中的协议部分即是scheme，比如http等。在<indent-filter>标签中可以使用<data>标签指定支持的scheme等五个属性。只有<data>标签指定的内容和Intent携带的Data完全一致的时候，才会启动活动。这里不一定要使用Uri，书上给的例子不太全面，具有一定的误导性，我在经过查询后得到以下的说明：

1. `Intent.ACTION_VIEW`是一个通用的做法，跨程序启动，或者说公用的接口都应该设置这个action_name
2. 匹配的是Data，可以通过任何方法生成Uri，比如`Uri.fromFile`可以获取文件的修饰符，我在下面给出一个来自StackOverflow的而关于启动pdf文件的例子。

```java
File file = new File(Environment.getExternalStorageDirectory().getAbsolutePath() +"/"+ filename);
Intent target = new Intent(Intent.ACTION_VIEW);
target.setDataAndType(Uri.fromFile(file),"application/pdf");
target.setFlags(Intent.FLAG_ACTIVITY_NO_HISTORY);

Intent intent = Intent.createChooser(target, "Open File");
try {
    startActivity(intent);
} catch (ActivityNotFoundException e) {
    // Instruct the user to install a PDF reader here, or something
}   
```

### 传递数据

1. 使用`putExtra(String key, Object value)`可以传递数据给下一个活动
2. 在下一个活动中使用`getIntent`获取Intent，`getExtra`获取数据
4. 首先在上一个活动需要调用`startActivityForResult`方法
5. 返回数据**同样需要借助Intent**，启动的活动需要**用无参构造函数**实例化一个Intent，设置数据后调用`setResult`方法
6. 上一个活动同时要重载`onActivityResult()`方法，这个方法会在目标活动结束后被毁掉

## 保存数据

活动在后台的时候可能随时被回收，但是回收前一定会调用`onSaveInstanceState()`方法，用来保存数据。保存的数据存在一个Bundel对象中，在onCreate被调用时作为参数传回。

## 生命周期和启动模式

这两部分书中讲解十分详细，不赘言。

## Wait for Update

1. 在AS中新建Activity默认继承的是`AppCompatActivity`，和`Activity`有什么区别？
2. 关于Menu的创建。
3. 关于标题栏的设置。