---
title: 快速学习语言系列--Java
date: 2023-1-10 23:10:54
tags: android
---

> 引言：本文帮助你快速建立对Java语言的认识和基本使用要点。
<!--more-->
# 一，Java简介
## 1. Java历史
Java是一门历史悠久，应用非常广泛的语言。1995年诞生，广泛应用于企业级web应用开发，andorid开发，及后台开发中。

## 2. Java的特点
Sun微系统对Java语言的解释是：“Java编程语言是个简单、**面向对象**、分布式、解释性、健壮、安全、与系统无关、可移植、高性能、多线程和动态的语言”。Java是一种强类型，编译型高级语言。

说起来Java，大部分人的第一印象是“一次编写，到处运行”的**跨平台特性**。它首先将源代码编译成字节码，再依赖各种不同平台上的虚拟机来解释执行字节码，从而具有“一次编写，到处运行”的跨平台特性。

除此以外，Java编程语言的风格十分接近C++语言。继承了C++语言面向对象技术的核心，舍弃了容易引起错误的指针，以引用取代；移除了C++中的运算符重载和多重继承特性，用接口取代；增加**垃圾回收器**功能。在Java SE 1.5版本中引入了**泛型编程**、类型安全的枚举、不定长参数和自动装/拆箱特性。

另，Java拥有最广泛的开源社区支持，各种高质量组件随时可用。

Java语言常年霸占着三大市场：
* 互联网和企业应用，这是Java EE的长期优势和市场地位
* 大数据平台，主要有Hadoop、Spark、Flink等，他们都是Java或Scala（一种运行于JVM的编程语言）开发的；
* Android移动平台。


## 3. Java开发基本认知
### 1）Java版本
Java官方提供三个大版本，Java EE/SE/ME。

简单来说，Java SE就是标准版，包含标准的JVM和标准库，而Java EE是企业版，它只是在Java SE的基础上加上了大量的API和库，以便方便开发Web应用、数据库、消息服务等，Java EE的应用使用的虚拟机和Java SE完全相同。
Java ME就和Java SE不同，它是一个针对嵌入式设备的“瘦身版”，Java SE的标准库无法在Java ME上使用，Java ME的虚拟机也是“瘦身版”。
小版本列表如下（以SE为例）：
```
时间	版本
1995	1.0
1998	1.2
2000	1.3
2002	1.4
2004	1.5 / 5.0
2005	1.6 / 6.0
2011	1.7 / 7.0
2014	1.8 / 8.0
2017/9	1.9 / 9.0
2018/3	10
2018/9	11
2019/3	12
2019/9	13
2020/3	14
2020/9	15
2021/3	16
2021/9	17
2022/3	18
2022/9	19
2023/3	20
2023/9	21
```
### 2）Java运行机制
#### 2.1) JDK和JRE
Java文件被编译成Java中间字节码（.class）之后，运行在各个平台的JRE(java runtime)上。

这里面涉及两个概念：JRE和JDK
* JDK：Java Development Kit
* JRE：Java Runtime Environment

简单地说，JRE就是运行Java字节码的虚拟机。但是，如果只有Java源码，要编译成Java字节码，就需要JDK，因为JDK除了包含JRE，还提供了编译器、调试器等开发工具。

二者关系如下：
```
  ┌─    ┌──────────────────────────────────┐
  │     │     Compiler, debugger, etc.     │
  │     └──────────────────────────────────┘
 JDK ┌─ ┌──────────────────────────────────┐
  │  │  │                                  │
  │ JRE │      JVM + Runtime Library       │
  │  │  │                                  │
  └─ └─ └──────────────────────────────────┘
        ┌───────┐┌───────┐┌───────┐┌───────┐
        │Windows││ Linux ││ macOS ││others │
        └───────┘└───────┘└───────┘└───────┘
```
我们运行Java前，需要去Oracal官网下载最新稳定版本JDK，安装及设置环境变量JAVA_HOME。

如果想了解具体JDK的内容，可以去官网下载对应版本的OpenJDK，即JDK的开放源码。

#### 2.2) Java命令行和IDE
常见命令：
* java：这个可执行程序其实就是JVM，运行Java程序，就是启动JVM，然后让JVM执行指定的编译后的代码；
* javac：这是Java的编译器，它用于把Java源码文件（以.java后缀结尾）编译为Java字节码文件（以.class后缀结尾）；
* jar：用于把一组.class文件打包成一个.jar文件，便于发布；
* javadoc：用于从Java源码中自动提取注释并生成文档；
* jdb：Java调试器，用于开发阶段的运行调试。

常见IDE：
Eclipse，IntelliJ。
如果是Android开发，那就是android studio。

# 二，Java的基本用法
## 1. 数据类型
### 1）基本类型
* 整数类型：byte(1字节)，short(2)，int(4)，long(8)
* 浮点数类型：float(4)，double(8)
* 字符类型：char(2)
* 布尔类型：boolean(1)
var关键字：编译器会根据赋值语句自动推断出变量类型。

### 2）引用类型
指向String，Array等任意对象的变量，都是引用类型。引用类型的变量相当于指针。

## 2. 类
注意区分2个概念：类和实例。这里不再展开。
注意：类中的成员必须初始化，否则编译器会提示报错。（因为JVM的类加载过程中字节码校验阶段会去检查）
### 1）作用域
包package->类class->function+field;
三者的包含关系如上，作用域的划分也是如此。当我们需要访问某个别的package下的class的时候，可以直接使用完整的package.class的方式，或者import package.class的方式。

其中class，function，field都可以用public, protected, private修饰符界定访问作用域。

### 2）方法及构造方法
#### 2.1）入口方法
可执行程序默认的入口方法就是这个全局唯一的static void main函数。
```
public class Main {
    public static void main(String[] args) {
        ...
    }
}
```
#### 2.2）构造方法
构造方法存在的意义是用来方便一次性初始化类中多个内部字段的。一般来说内部字段是private，构造函数和对外接口函数是public的。初始化的执行顺序是先执行字段的赋值初始化（如果没有，编译器会赋默认初始值0，false,null等），再执行构造函数。

我们可以显式的创建，但如果我们不创建，编译器也会给每个类一个不带参数的默认构造函数（内部逻辑为空）；一旦我们显式创建，编译器就不会默认创建了。

构造方法可以有一组重载的，只是参数不同；如果是继承子类，构造函数内部逻辑记得优先调用父类的某个构造函数super()/super(xxx);

### 3）方法重载overload
重载就是一组方法的名称一样，签名不同（参数个数和类型）；一般来说重载方法返回类型是相同的。

### 4）继承和多态
#### 4.1）继承
引入父类和子类的概念，对应关键字extends。
* 一个子类只能继承一个父类，但是可以形成继承链，所有类最终继承自Object。
* 子类可以直接访问父类所有protected和public的字段和方法；
* 子类可以选择性override覆写父类protected和public的方法；
* final关键字：一个final类不能被继承；一个final方法不能被override；一个final字段一旦初始化不能被修改。
* 父类指针可以指向子类对象

#### 4.2）多态
因为子类可以覆写父类方法，且父类指针可以指向子类对象。所以当我们调用一个父类指针指向的类中的某个方法时，我们并不知道，他究竟指向的是谁，会走到哪个方法里。（可以通过instanceof确认）

多态的特性就是，运行期才能动态决定调用的子类方法。多态是java面向对象设计中的一大特点，他的好处是增强了对象的可扩展性和使用灵活性。

### 5）抽象类和接口
#### 5.1）抽象类和抽象方法
对应关键字abstract。抽象方法是不需要写实现的；抽象类是无法实例化的。
* 只要类中有一个抽象方法，这个类只能是抽象类
* 抽象类中也可以有正常的方法和字段
* 抽象类存在的意义就是为了给子类定义规范和公共逻辑。

#### 5.2）接口
接口interface是更纯粹的抽象类，无法实例化，所有方法都默认是抽象方法。
* 一个子类只能继承一个父类，但是可以实现implements多个接口。
* 接口存在的意义就是给子类定义规范
* 接口中的字段默认都是public static final的。

但是注意，java8中引入了一个特例：接口中的default方法。default方法的实现在接口中，而不是实现类中。default方法的引入是为了解决已发布的版本中，给接口添加新方法而不影响已有实现的问题。

### 6）静态字段和方法
类中的静态static字段和方法，说明这个字段和方法属于类，而不是实例。所有该类的实例共享同一个，调用方式是类名.方法名/字段名。

### 7）内部类
Java的内部类可分为Inner Class、Anonymous Class和Static Nested Class三种：
```
//Inner Class
public class Main {
    public static void main(String[] args) {
        Outer outer = new Outer("Nested"); // 实例化一个Outer
        Outer.Inner inner = outer.new Inner(); // 实例化一个Inner
        inner.hello();
    }
}

class Outer {
    private String name;

    Outer(String name) {
        this.name = name;
    }

    class Inner {
        void hello() {
            System.out.println("Hello, " + Outer.this.name);
        }
    }
}
```
```
//Anonymous Class
class Outer {
    private String name;

    Outer(String name) {
        this.name = name;
    }

    void asyncHello() {
        Runnable r = new Runnable() {
            @Override
            public void run() {
                System.out.println("Hello, " + Outer.this.name);
            }
        };
        new Thread(r).start();
    }
}

```
```
// Static Nested Class
public class Main {
    public static void main(String[] args) {
        Outer.StaticNested sn = new Outer.StaticNested();
        sn.hello();
    }
}

class Outer {
    private static String NAME = "OUTER";

    private String name;

    Outer(String name) {
        this.name = name;
    }

    static class StaticNested {
        void hello() {
            System.out.println("Hello, " + Outer.NAME);
        }
    }
}

```

* Inner Class和Anonymous Class本质上是相同的，都必须依附于Outer Class的实例，即隐含地持有Outer.this实例，并拥有Outer Class的private访问权限；
* Static Nested Class是独立类，但拥有Outer Class的private访问权限。

## 3. Java核心类
### 1）String
String是一个引用类型，他本身也是一个类，可以用"..."表示一个字符串；多行字符串用"""..."""。
* Java字符串String是不可变对象；
* 字符串操作不改变原字符串内容，而是返回新字符串；
* 常用的字符串操作：分割split拼接join、比较equal、替换replace、大小写转换等；

String相关类型转换：
* 其他任意基本/引用类型转成string(String.valueOf(xxx)，new String(char[]));
* string转成其他类型(Integer.parseInt,Boolean.parseBoolean，toCharArray())

其他String相关常用核心类StringBuilder和StringJoiner。
### 2）AutoBox/UnBoxing
Java为每个基本类型都提供了一个包装类型，这么做的目的是为了提供大量的实用方法。
为了方便使用，java1.5之后提供了自动装箱和拆箱（编译器完成）。自动装箱和拆箱的的好处很明显，但是坏处是比较影响执行效率。

* 所有的包装类型都是不变类，一旦创建就不能改变
* 包装类型的比较必须使用equals()；
* 包装类型提供的常见方法：进制转换，类型转换，有符号数转成无符号数（java中没有无符号基本类型，但是和c++交互的时候需要转成无符号数）。
### 3）JavaBean vs POJO
这两个其实非常类似，都是为了实现一个可重用组件。只不过POJO更纯粹一些。
POJO: 有一些private的参数作为对象的属性，然后针对每一个参数定义get和set方法访问的接口。没有从任何类继承、也没有实现任何接口，更没有被其它框架侵入的java对象。
JavaBean：JavaBean是要符合一定编写规范的。这些规范是：
* 所有属性为private。
* 这个类必须有一个公共的缺省构造函数。即是提供无参数的构造器。
* 这个类的属性使用getter和setter来访问，其他方法遵从标准命名规范。
* 这个类应是可序列化的。实现serializable接口。
JavaBean是可以有其他的无关数据的方法的。
总结下来，两者的区别是：
1，POJO其实是比javabean更纯净的简单类或接口。POJO严格地遵守简单对象的概念，而一些JavaBean中往往会封装一些简单逻辑。
2，POJO主要用于数据的临时传递，它只能装载数据， 作为数据存储的载体，而不具有业务逻辑处理的能力。
3，Javabean虽然数据的获取与POJO一样，但是javabean当中可以有其它的方法。

### 4）enum
enum也是一种类，用于枚举常量。enum的特别之处在于：
* 直接用==比较，因为enum类型的每个常量在JVM中只有一个唯一实例。
* enum实例只能直接xx.xx赋值定义，不能new
* enum跟普通类一样可以用自己的变量、方法和构造函数，构造函数只能使用 private 访问修饰符，所以外部无法调用。

## 4. 异常
### 1）异常的分类
所有异常和错误类型都是Throwable类的子类，Throwable 类是层次结构的基类。其中一个分支以Exception为首。此类用于用户程序应捕获的异常情况。NullPointerException 就是此类异常的一个示例。另一个分支，Error由 Java 运行时系统（JVM）用来指示与运行时环境本身（JRE）有关的错误。StackOverflowError 就是此类错误的一个示例。
![](/img/16996080716412.jpg)

Exceptions又分为用户自定义异常和内置异常，内置异常里又分为检查和未检查异常。
* 检查异常：检查异常称为编译时异常，因为这些异常由编译器在编译时检查。
* 未检查异常：未检查异常与已检查异常相反。编译器在编译时不会检查这些异常。简单来说，如果一个程序抛出了一个未经检查的异常，即使我们没有处理或声明它，程序也不会给出编译错误。

![](/img/16996082640610.jpg)
### 2）JVM如何处理异常
简单来说，就是当方法内部发生异常的时候，该方法就会创建一个包含现场信息的异常对象，并传递给JVM。这一步叫抛出异常。
JVM收到异常对象后，开始在当前调用堆栈call stack中先找到发生异常的函数，然后按照调用顺序，反向搜查可以处理该异常的exception handler。
如果找到就交给exception handler处理，如果没有，就交给默认异常处理程序，该处理程序是运行时系统的一部分。该处理程序按以下格式打印异常信息并异常终止程序。
![](/img/16996089636600.jpg)
### 3）代码中捕获异常的原则
Error是无需捕获的，说明JVM出了问题，就应该fail fast，避免更多连锁问题。
Exception的捕获原则也是一样，如果是一些在设计中被认为不应该出现的，后续会产生不可预估连锁影响的异常，我们就应该遵循fail fast原则；如果异常是我们程序逻辑考虑的一部分，那就捕获它，并处理，保证它不影响主流程。

但是最忌讳的是为了规避编译器报错提醒，不加分别的捕获了但是不做处理。

## 5. 反射
### 1) 反射是什么？
首先反射是Java这个语言特有的一种特性。它允许运行中的 Java 程序获取自身的信息，并且可以操作类或对象的内部属性。
简而言之，通过反射，我们可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。程序中一般的对象的类型都是在编译期就确定下来的，而 Java 反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，即使这个对象的类型在编译期是未知的。

反射是RTTI(Run-Time Type Identification)的一种体现。RTTI是说JVM 在运行时才动态加载类或调用方法/访问属性，它不需要事先（编译期）知道运行对象是谁。

Java 反射主要提供以下功能：
* 在运行时判断任意一个对象所属的类；
* 在运行时构造任意一个类的对象；
* 在运行时判断任意一个类所具有的成员变量和方法（通过反射甚至可以调用private方法）；
* 在运行时调用任意一个对象的方法

重点：是运行时而不是编译时

### 2) 反射被设计用来解决什么场景？
业务开发中反射使用不多，框架及工具开发中会用到多一些。
* 使用 IDE(如 Eclipse，IDEA)时，当我们输入一个对象或类并想调用它的属性或方法时，一按点号，编译器就会自动列出它的属性或方法。这个就是用反射实现的。
* 开发通用框架时，很多框架（比如 Spring）都是配置化的（比如通过 XML 文件配置 Bean），为了保证框架的通用性，它们可能需要根据配置文件加载不同的对象或类，调用不同的方法，这个时候就必须用到反射，运行时动态加载需要加载的对象。

### 3) 反射应慎用
反射很强大，但不应该乱用。如果可以在不使用反射的情况下执行操作，那么最好避免使用它。通过反射访问代码时应牢记以下问题。

* 性能开销：由于反射涉及动态解析的类型，因此无法执行某些 Java 虚拟机优化。因此，反射操作的性能比非反射操作慢，因此应避免在性能敏感的应用程序中频繁调用的代码段中使用反射操作。
* 安全限制：反射需要运行时权限，在安全管理器下运行时可能不存在该权限。对于必须在受限安全上下文（例如 Applet）中运行的代码，这是一个重要的考虑因素。
* 内部结构暴露：由于反射允许代码执行在非反射代码中非法的操作（例如访问private字段和方法），因此使用反射可能会导致意外的副作用，这可能会导致代码功能失调并可能破坏可移植性。

### 4) 反射的用法？
 反射方法都在java.lang.relfect包中。具体使用中建议去查官方手册。这里仅简单列举：
 * 获取Class：Class.forName(str)，int.class，Integer.TYPE，str.getClass()
 * 判断是否为某个类的实例： Class 对象的 isInstance()方法
 * 创建实例：class.newInstance();c.getConstructor(String.class),constructor.newInstance("23333");
 * 获取方法和成员：getDeclaredMethods，getMethods，getDeclaredField，getField
 * 调用类的方法：method.invoke()
 * 获取继承关系：getSuperclass，getInterfaces，isAssignableFrom
 
## 6. 泛型
### 1）什么是泛型，好处是什么？
泛型可以按照c++的模板去理解，最大的作用就是扩展参数类型。泛型是一种编译期特性，编译器会进行类型擦除，最终运行的时候是没有泛型这一说的。很多Java的容器类例如HashSet、ArrayList、HashMap等类就很好地使用了泛型来扩展参数类型。

### 2）泛型的用法
泛型的用法这里不具体列出，泛型的使用中要注意以下几点：
1, 泛型方法中的泛型可以是方法的参数类型，返回值类型
2, 一个泛型方法中可以存在多个泛型(k,v,T,?)，内部方法体和普通方法一样
3, 泛型的类型可以是**有界**的(<String>/下届<T extends Number>/上届<? super T>)。
   * 有界的意思是有类型限定的，比如必须是某种类型或者必须继承或者父类必须是谁，可以多类型限定。
   * 如果没有注明类型，即**无界**的(<>)，就默认是<Object>
   * 泛型不能是基本类型

4, 泛型是**编译期特性**，具体来说编译期会进行**类型擦除**，也就是把泛型替换成具体类型。
   具体来说，就是有界的泛型替换成指定类型，无界的泛型替换成Object。

## 7. 集合
Java的集合可以理解为Java已经帮你实现好的一些高阶数据结构。
* 他们都存在于Java标准库java.util中，都支持泛型。
* Collection是除Map外所有其他集合类的根接口，主要的3种集合类是List，Set和Map。
* Java集合使用统一的Iterator遍历。
* 实现了接口和实现类相分离。例如，有序表的接口是List，具体的实现类有ArrayList，LinkedList等

### 1)List
List是接口，按索引顺序访问的长度可变的有序表。
 * 常用的实现是ArrayList和LinkedList，前者是基于数组的，后者是基于链表的。大家根据使用场景效率选择。
 * 可以直接使用for each遍历List
 * List可以和Array相互转换
### 2)Set
Set是接口，用于存储不重复的元素集合。
* 常用的实现是HashSet和TreeSet，SortedSet
* 遍历SortedSet按照元素的排序顺序遍历，也可以自定义排序算法
### 3)Map
Map也是接口，kv对映射表。
 * 常见实现是HashMap
 * 可以通过for each遍历keySet()，也可以通过for each遍历entrySet()，直接获取key-value
 * 可以直接使用for each遍历List
### 4)其他
其他经常用到的集合还有Queue, Stack等等。

## 8. IO
Java的IO分为同步和异步，分别是标准库java.io和java.nio提供支持。
### 1）同步IO
操作对象分为两类：
 * 字节流接口：InputStream/OutputStream；
 * 字符流接口：Reader/Writer
 具体接口及使用这里不再展开。
 
### 2）异步IO
（ToDo：待填坑）

## 9. 函数式编程
### 1）什么是函数式编程
首先函数式编程是lambda演算的一种应用体现。
* 那么什么是lambda演算呢？
  lambda演算是一种**计算模型**，从数学逻辑中发展，以变数绑定和替换的规则，来研究函式如何抽象化定义，进而用函数来定义数据和指令的。
  与lambda演算平行的一种计算模型是图灵机，就能力范围而言，这两者是等价的，他们都是图灵完备的。也就是说图灵机能够实现的，lambda演算也能够实现，反之亦然。
* 那lambda演算和图灵机两种计算模型的差别是什么呢？
  lambda演算本质是基于**变换规律**的，表示变换规律的函数是一切的基础，指令和数据都是基于函数定义的；图灵机是基于状态的，先定义数据（状态），然后根据已有规律定义指令（状态变化规律）。前者是数学化规律描述，可以预测后续的事情，更适合AI这种用数学规律预测没发生的事情的场景；后者是工程化描述，描述的是已经发生过的规律，更适合工程场景。
  但是也不是说lambda演算就一定优于图灵机，只是说彼此的优势点不同，lambda演算也有不如图灵机的场景，比如lambda演算架构实现起来更复杂，如果工程开发过程中遇到了问题，图灵机模型构建的程序是可以通过小修小补解决的，但是对于lambda演算程序而言，就可能是大问题，可能会推翻整个设立从头来，因为说明这个规律是有问题的。
* 那函数式编程，过程式编程都是什么呢？
  首先我们要明确一个前提，现代计算机经过历史的演变，最终选择了图灵机模型来实现，也就是图灵架构。但是介于lambda演算如上文所说，有优势的地方，所以现代很多高级语言的设计中是两者思想的融合，也就是很多语言提供了函数式编程（背后是lambda演算的体现）。这里要注意的是lambda演算的实现最终也是在图灵机上实现的。
  函数式编程在高阶语言中，其实已经被封装好，我们使用中不涉及去重新创建一个新的规律，而只是使用。从这个层面，它的使用规范是过程式编程是一样的，都是固定的。它的特征就是函数是头等对象一个函数，既可以作为其它函数的输入参数值，也可以从函数中返回值，被修改或者被分配给一个变量。
  而图灵机的特征就是数据+指令，也就是面向过程编程的思想。
* 那面向对象编程又是什么呢？
  面向对象编程是过程式编程的进一步封装，目的是对图灵机的编程自由进一步约束。
  从意义上讲，面向对象，函数式编程都是对图灵机的编程自由进行约束，同时不影响它的能力解决上限。冯诺依曼架构(冯诺依曼架构和哈佛架构都是基于图灵机的)中数据和指令都加载到内存里，是不区分谁是谁的，所以才会有boot程序，但是更上层的开发中这么搞就很危险，开发者可以随便往内存或者操作指令。所以linux系统在软件层面在进程的内存划分中区分了指令段和数据段，前者是不可操作的，这样子就限制了一部分自由度；更往上高级语言层面也做了更多的自由限制，比如禁掉了goto语句，用循环判断代替，防止随意操作到不可控的内存地址；更往上面向对象层面就是把数据和操作进行了一定程度的绑定，这些操作只针对这种数据，进一步限制了编程自由度；函数式编程是只要输入是固定的，输出就是固定的，禁止了一种输入多种输出的破序问题，也是一种编程自由度的限制。

### 2）Java函数式编程的用法
从Java 8开始引入了lambda表达式。简单来说，lambda表达式可以理解定义函数接口(FunctionalInterface)实现的快捷方式。让我们可以直接定义一个函数接口的实现，而不是先用一个类实现这个接口，再传递这个类的实例(如代码中的cat)。其中这个抽象方法的参数和类型不用声明，因为编译器会自行判断具体类型，抽象方法可以有多个参数及返回值。
#### 2.1) FunctionalInterface  
函数式接口FunctionalInterface是一种注解，表示这个接口中只有一个抽象方法（可以有default方法和static方法）。lambda表达式对应的必须是只有一个抽象方法的接口，否则编译器不知道Printable赋值的实现是对应那个抽象方法的。

常见的系统函数式接口有Comparator，Runnable，Callable。

FunctionalInterface允许传入的参数并不限于lambda表达式，共有：
* 接口的实现类（传统写法，代码较繁琐）；
* Lambda表达式（只需列出参数名，由编译器推断类型）；
* 符合方法签名的静态方法；
* 符合方法签名的实例方法（实例类型被看做第一个参数类型）；
* 符合方法签名的构造方法（实例类型被看做返回类型）。
FunctionalInterface不强制继承关系，不需要方法名称相同，只要求方法参数（类型和数量）与方法返回类型相同，即认为方法签名相同。
```
@FunctionalInterface
interface Printable{
    String print(String suffix);
} 

class Cat implements Printable{
    public String name;
    public int age;
    public String print(String suffix){
        return "Meow " + suffix;
    }
}

class HelloWorld {
    public static void main(String[] args) {
        // Cat a = new Cat();
        // printThing(a);
        Printable tmp = (s)->{return "Meow " + s;};
        printThing(tmp);
    }
    
    static void printThing(Printable p){
         System.out.println(p.print("!"));
    }
}
```
```
//Comparator接口的lambda表达式用法
public class Main {
    public static void main(String[] args) {
        String[] array = new String[] { "Apple", "Orange", "Banana", "Lemon" };
        Arrays.sort(array, (s1, s2) -> {
            return s1.compareTo(s2);
        });
        System.out.println(String.join(", ", array));
    }
}
```
#### 2.1)  Stream
函数式编程还有一处经典应用，是在Stream API中，Stream API也是Java 8中引入。它位于java.util.stream包中，它代表的是任意Java对象的序列。Stream输出的元素可能并没有预先存储在内存中，而是实时计算出来的。也就是说，Stream和List的区别是List的用途是操作一组已存在的Java对象，而Stream实现的是惰性计算。
 Stream API的基本用法就是：创建一个Stream，然后做若干次转换，最后调用一个求值方法获取真正计算的结果。
```
Stream<BigInteger> naturals = createNaturalStream();// 全体自然数
naturals.map(n -> n.multiply(n)) // 1, 4, 9, 16, 25...
        .limit(100)
        .forEach(System.out::println);
```
我们总结一下Stream类的特点：
* 它可以“存储”有限个或无限个元素(元素有可能已经全部存储在内存中，也有可能是根据需要实时计算出来的)
* 一个Stream可以轻易地转换为另一个Stream，而不是修改原Stream本身
* 一个Stream转换为另一个Stream时，实际上只存储了转换规则，并没有任何计算发生。例如上述代码只有在forEach的时候才真正的运算。

总结下Stream API的特点是：
* Stream API提供了一套新的流式处理的抽象序列；
* Stream API支持函数式编程和链式操作；
* Stream可以表示无限序列，并且大多数情况下是惰性求值的。

Stream提供的常用操作有：
* 转换操作：map()，filter()，sorted()，distinct()；
* 合并操作：concat()，flatMap()；
* 并行处理：parallel()；
* 聚合操作：reduce()，collect()，count()，max()，min()，sum()，average()；
* 其他操作：allMatch(), anyMatch(), forEach()。

## 10. 单元测试
单元测试的好处这里就不说了，这里介绍下Java平台最常用的测试框架JUnit。
JUnit既可以直接在IDE中运行，也可以方便地集成到Maven这些自动化工具中运行。
在编写单元测试的时候，我们要遵循一定的规范：
* 一是单元测试代码本身必须非常简单，能一下看明白，决不能再为测试代码编写测试；
* 二是每个单元测试应当互相独立，不依赖运行的顺序；
* 三是测试时不但要覆盖常用测试用例，还要特别注意测试边界条件，例如输入为0，null，空字符串""等情况。

JUnit测试的使用方法：
* @Test方法，并使用Assertions进行断言，注意浮点数assertEquals()要指定delta
* 编写Fixture：针对每个@Test方法，编写@BeforeEach方法用于初始化测试资源，编写@AfterEach用于清理测试资源；必要时，可以编写@BeforeAll和@AfterAll，使用静态变量来初始化耗时的资源，并且在所有@Test方法的运行前后仅执行一次。
* 对可能发生的每种类型的异常都必须进行测试：使用assertThrows()，期待捕获到指定类型的异常
* 条件测试是根据某些注解在运行期让JUnit自动忽略某些测试
* 参数化测试：可以在测试代码中写死，也可以通过@CsvFileSource放到外部的CSV文件中

## 11. 多线程
Java语言内置了多线程支持：一个Java程序实际上是一个JVM进程，JVM进程用一个主线程来执行main()方法，在main()方法内部，我们又可以启动多个线程。此外，JVM还有负责垃圾回收的其他工作线程等。
### 1）线程的创建和状态
#### 1.1）创建和状态
通过new Thread创建，内部实现run接口（或直接lambda表达式），也即线程的执行代码。start接口线程开始运行。
线程调度由操作系统决定，程序本身无法决定调度顺序。
```
public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            Thread.setPriority(10); // 1~10, 默认值5
            System.out.println("start new thread!");
        });
        t.start(); // 启动新线程
    }
}
```
Java线程的状态有以下几种：
* New：新创建的线程，尚未执行；
* Runnable：运行中的线程，正在执行run()方法的Java代码；
* Blocked：运行中的线程，因为某些操作被阻塞而挂起；
* Waiting：运行中的线程，因为某些操作在等待中；
* Timed Waiting：运行中的线程，因为执行sleep()方法正在计时等待；
* Terminated：线程已终止，因为run()方法执行完毕。
```
         ┌─────────────┐
         │     New     │
         └─────────────┘
                │
                ▼
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
 ┌─────────────┐ ┌─────────────┐
││  Runnable   │ │   Blocked   ││
 └─────────────┘ └─────────────┘
│┌─────────────┐ ┌─────────────┐│
 │   Waiting   │ │Timed Waiting│
│└─────────────┘ └─────────────┘│
 ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
                │
                ▼
         ┌─────────────┐
         │ Terminated  │
         └─────────────┘
```
#### 1.2）终止线程
线程终止的原因有：
* 线程正常终止：run()方法执行到return语句返回或者join等待某个线程后自动终止；
* 线程意外终止：run()方法因为未捕获的异常导致线程终止；
* 对某个线程的Thread实例调用stop()方法强制终止（强烈不推荐使用）。
**那如何主动中断一个线程呢？**
1, 对目标线程调用interrupt()方法可以请求中断一个线程，目标线程通过检测isInterrupted()标志获取自身是否已中断。如果目标线程处于等待状态，该线程会捕获到InterruptedException。
2, 设置标志位，标志位用线程间共享的变量用关键字volatile声明。
```
//interrupt()方法仅仅向t线程发出了“中断请求”，t线程响应时间看系统调度
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new MyThread();
        t.start();
        Thread.sleep(1000);
        t.interrupt(); // 中断t线程
        t.join(); // 等待t线程结束
        System.out.println("end");
    }
}

class MyThread extends Thread {
    public void run() {
        Thread hello = new HelloThread();
        hello.start(); // 启动hello线程
        try {
            hello.join(); // 等待hello线程结束
        } catch (InterruptedException e) {
            System.out.println("interrupted!");
        }
        hello.interrupt();
    }
}

class HelloThread extends Thread {
    public void run() {
        int n = 0;
        while (!isInterrupted()) {
            n++;
            System.out.println(n + " hello!");
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                break;
            }
        }
    }
}
```
```
//设置标志位running
public class Main {
    public static void main(String[] args)  throws InterruptedException {
        HelloThread t = new HelloThread();
        t.start();
        Thread.sleep(1);
        t.running = false; // 标志位置为false
    }
}

class HelloThread extends Thread {
    public volatile boolean running = true;
    public void run() {
        int n = 0;
        while (running) {
            n ++;
            System.out.println(n + " hello!");
        }
        System.out.println("end!");
    }
}

```
#### 1.3）volatile原理
这涉及到Java的内存模型。在Java虚拟机中，变量的值保存在主内存中，但是，当线程访问变量时，它会先获取一个副本，并保存在自己的工作内存中。如果线程修改了变量的值，虚拟机会在某个时刻把修改后的值回写到主内存，但是，这个时间是不确定的！
因此，volatile关键字的目的是告诉虚拟机：
* 每次访问变量时，总是获取主内存的最新值；
* 每次修改变量后，立刻回写到主内存。
volatile关键字解决的是数据一致性问题：当一个线程修改了某个共享变量的值，其他线程能够立刻看到修改后的值。使用volatile比使用锁代价更小。

### 2）守护线程
因为只有有一个线程不结束，JVM进程就不会退出。但是实际业务场景里，我们有一些不需要关注的后台线程，并不会主动去关闭它，例如gc线程。
这种情况下就把它设置为守护线程，所有非守护线程都执行完毕后，无论有没守护线程，虚拟机都直接退出。
在守护线程中，编写代码要注意：守护线程不能持有任何需要关闭的资源，例如打开文件等，因为虚拟机退出时，守护线程没有任何机会来关闭文件，这会导致数据丢失。
```
Thread t = new MyThread();
t.setDaemon(true);
t.start();
```
### 3）线程同步
多线程同时操作共享变量时势必会带来数据一致性问题。除了上文提到的volatile关键字，更通用的方式是加锁，将临界区（修改共享变量的线程代码块）锁起来。

注意有些原子操作是不需要锁的：
JVM规范定义了几种原子操作：
* 基本类型（long和double除外）赋值，例如：int n = m；
* 引用类型赋值，例如：List<String> list = anotherList。
* 后面会提到的Atomic类，也是底层实现并封装好的原子操作。
* 读写一个不可变对象，如String，Integer，LocalDate

最常见的锁就是synchronized锁，使用方法是：
1,找出修改共享变量的线程代码块；
2,选择一个共享实例作为锁；
3,使用synchronized(lockObject) { ... }。
在使用synchronized的时候，不必担心抛出异常。因为无论是否有异常，都会在synchronized结束处正确释放锁.
```
public class Main {
    public static void main(String[] args) throws Exception {
        var add = new AddThread();
        var dec = new DecThread();
        add.start();
        dec.start();
        add.join();
        dec.join();
        System.out.println(Counter.count);
    }
}

class Counter {
    public static final Object lock = new Object();
    public static int count = 0;
}

class AddThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized(Counter.lock) {
                Counter.count += 1;
            }
        }
    }
}

class DecThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized(Counter.lock) {
                Counter.count -= 1;
            }
        }
    }
}

```
注意synchronized一般直接对类的实例或者类本身加锁。
```
void set(String[] names, int n) {
    // 局部变量其他线程不可见:
    List<String> ns = List.of(names);
    int step = n * 10;
    synchronized(this) {
        this.names = ns;
        this.x += step;
        this.y += step;
    }
}
synchronized void set(String[] names, int n) {
   ...
}
synchronized void static set(String[] names, int n) {
   ...
}

```
一个类如果可以被多个线程安全的访问（eg所有变量的访问public方法都加锁了，或者成员变量都是final的（不变类，eg:String），或者只有静态方法的工具类（eg:Math）），我们叫做**线程安全的类**。
 没有特殊说明时，一个类默认是非线程安全的。
 
### 4）锁
这里聊聊Java里常用的锁：
1，synchronized锁，可重入锁。结合wait/notify进行多线程协调运行。
wait/notify只能在synchronized块中锁对象上调用。其中wait()方法的执行机制非常复杂。首先，它不是一个普通的Java方法，而是定义在Object类的一个native方法，也就是由JVM的C代码实现的。其次，必须在synchronized块中才能调用wait()方法，因为wait()方法调用时，会释放线程获得的锁，wait()方法返回后，线程又会重新试图获得锁。

2，ReentrantLock锁，可重入锁，tryLock()失败的时候不会导致死锁，比synchronized更安全。结合Condition进行多线程协调运行。
3，ReadWriteLock锁，适用场景同一个数据，有大量线程读取，但仅有少数线程修改。
4，StampedLock锁，Java 8引入的升级版ReadWriteLock，优势在于读锁是一种乐观锁。显然乐观锁的并发效率更高，但一旦有小概率的写入导致读取的数据不一致，需要能检测出来，再读一遍就行。
```
//synchronized+wait/notify
class TaskQueue {
    Queue<String> queue = new LinkedList<>();

    public synchronized void addTask(String s) {
        this.queue.add(s);
        this.notifyAll();
    }

    public synchronized String getTask() throws InterruptedException {
        while (queue.isEmpty()) {
            this.wait();
        }
        return queue.remove();
    }
}
```
```
//ReentrantLock+Condition
class TaskQueue {
    private final Lock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    private Queue<String> queue = new LinkedList<>();

    public void addTask(String s) {
        lock.lock();
        try {
            queue.add(s);
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }

    public String getTask() {
        lock.lock();
        try {
            while (queue.isEmpty()) {
                condition.await();
            }
            return queue.remove();
        } finally {
            lock.unlock();
        }
    }
}
```
```
//ReadWriteLock
public class Counter {
    private final ReadWriteLock rwlock = new ReentrantReadWriteLock();
    private final Lock rlock = rwlock.readLock();
    private final Lock wlock = rwlock.writeLock();
    private int[] counts = new int[10];

    public void inc(int index) {
        wlock.lock(); // 加写锁
        try {
            counts[index] += 1;
        } finally {
            wlock.unlock(); // 释放写锁
        }
    }

    public int[] get() {
        rlock.lock(); // 加读锁
        try {
            return Arrays.copyOf(counts, counts.length);
        } finally {
            rlock.unlock(); // 释放读锁
        }
    }
}
```
```
//StampedLock
public class Point {
    private final StampedLock stampedLock = new StampedLock();

    private double x;
    private double y;

    public void move(double deltaX, double deltaY) {
        long stamp = stampedLock.writeLock(); // 获取写锁
        try {
            x += deltaX;
            y += deltaY;
        } finally {
            stampedLock.unlockWrite(stamp); // 释放写锁
        }
    }

    public double distanceFromOrigin() {
        long stamp = stampedLock.tryOptimisticRead(); // 获得一个乐观读锁
        // 注意下面两行代码不是原子操作
        // 假设x,y = (100,200)
        double currentX = x;
        // 此处已读取到x=100，但x,y可能被写线程修改为(300,400)
        double currentY = y;
        // 此处已读取到y，如果没有写入，读取是正确的(100,200)
        // 如果有写入，读取是错误的(100,400)
        if (!stampedLock.validate(stamp)) { // 检查乐观读锁后是否有其他写锁发生
            stamp = stampedLock.readLock(); // 获取一个悲观读锁
            try {
                currentX = x;
                currentY = y;
            } finally {
                stampedLock.unlockRead(stamp); // 释放悲观读锁
            }
        }
        return Math.sqrt(currentX * currentX + currentY * currentY);
    }
}

```
### 5）信号量
一种受限资源，它需要保证同一时刻最多有N个线程能访问，比如同一时刻最多创建100个数据库连接，最多允许10个用户下载等。
这种限制数量的锁，如果用Lock数组来实现，就太麻烦了。
这种情况就可以使用Semaphore, 例如，最多允许3个线程同时访问：
```
public class AccessLimitControl {
    // 任意时刻仅允许最多3个线程获取许可:
    final Semaphore semaphore = new Semaphore(3);

    public String access() throws Exception {
        // 如果超过了许可数量,其他线程将在此等待:
        semaphore.acquire();
        try {
            // TODO:
            return UUID.randomUUID().toString();
        } finally {
            semaphore.release();
        }
    }
}

```
### 6）Concurrent集合
使用java.util.concurrent包提供的线程安全的并发集合可以大大简化多线程编程，尽量使用标准库的并发集合，避免自己编写同步代码。
![](/img/17001089528176.jpg)

### 7）Atomic
一组原子操作的封装类，它们位于java.util.concurrent.atomic包。
Atomic类是通过无锁（lock-free）的方式实现的线程安全（thread-safe）访问。它的主要原理是利用了CAS：Compare and Set(利用CMPXCHG指令实现，它是一个原子指令)。适用于计数器，累加器等。

### 8）线程池
Java标准库提供了ExecutorService接口表示线程池。提供的几个常用实现类有：
* FixedThreadPool：线程数固定的线程池；
* CachedThreadPool：线程数根据任务动态调整的线程池；
* SingleThreadExecutor：仅单线程执行的线程池。
如果我们希望提交到线程池的任务可以返回值，那么就对对线程池提交一个Callable任务，可以获得一个Future对象，Future在将来某个时刻获取结果。
```
ExecutorService executor = Executors.newFixedThreadPool(4); 
// 定义任务:
Callable<String> task = new Task();
// 提交任务并获得Future:
Future<String> future = executor.submit(task);
// 从Future获取异步执行返回的结果:
String result = future.get(); // 可能阻塞
```
Future的不好在于一旦调用get，会阻塞主线程。所以Java 8开始引入了CompletableFuture，它针对Future做了改进，可以传入回调对象，当异步任务完成或者发生异常时，自动调用回调对象的回调方法。
CompletableFuture可以指定异步处理流程：
* thenAccept()处理正常结果；
* exceptional()处理异常结果；
* thenApplyAsync()用于串行化另一个CompletableFuture；
* anyOf()和allOf()用于并行化多个CompletableFuture。

### 10）ForkJoin
ava 7开始引入了一种新的Fork/Join线程池，它更好的支持并行计算，更好的支持多核cpu场景。
Fork/Join是一种基于“分治”的算法：通过分解任务，并行执行，最后合并结果得到最终结果。ForkJoinPool线程池可以把一个大任务分拆成小任务并行执行，任务类必须继承自RecursiveTask或RecursiveAction。
```
class SumTask extends RecursiveTask<Long> {
    protected Long compute() {
        // “分裂”子任务:
        SumTask subtask1 = new SumTask(...);
        SumTask subtask2 = new SumTask(...);
        // invokeAll会并行运行两个子任务:
        invokeAll(subtask1, subtask2);
        // 获得子任务的结果:
        Long subresult1 = subtask1.join();
        Long subresult2 = subtask2.join();
        // 汇总结果:
        return subresult1 + subresult2;
    }
}
//使用代码
// fork/join:
        ForkJoinTask<Long> task = new SumTask(array, 0, array.length);
        Long result = ForkJoinPool.commonPool().invoke(task);
```
### 11）ThreadLocal
ThreadLocal表示线程的“局部变量”，它确保每个线程的ThreadLocal变量都是各自独立的；ThreadLocal适合场景是在一个线程的处理流程中保持上下文（避免了同一参数在所有方法中传递）。
```
public class UserContext implements AutoCloseable {

    static final ThreadLocal<String> ctx = new ThreadLocal<>();

    public UserContext(String user) {
        ctx.set(user);
    }

    public static String currentUser() {
        return ctx.get();
    }

    @Override
    public void close() {
        ctx.remove();
    }
}
//使用代码
try (var ctx = new UserContext("Bob")) {
    // 可任意调用UserContext.currentUser():
    String currentUser = UserContext.currentUser();
} 
```
### 12）虚拟线程
虚拟线程（Virtual Thread）是Java 19引入的一种轻量级线程，它在很多其他语言中被称为协程、纤程、绿色线程、用户态线程等。
虚拟线程是为了解决IO密集型任务的吞吐量，它可以高效通过少数线程去调度大量虚拟线程；
虚拟线程在执行到IO操作或Blocking操作时，会自动切换到其他虚拟线程执行（JDK自动的，无需主动await），从而避免当前线程等待，能最大化线程的执行效率；
虚拟线程使用普通线程相同的接口，最大的好处是无需修改任何代码，就可以将现有的IO操作异步化获得更大的吞吐能力。
计算密集型任务不应使用虚拟线程，只能通过增加CPU核心解决，或者利用分布式计算资源。
```
// 创建调度器:
ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor();
// 创建大量虚拟线程并调度:
ThreadFactory tf = Thread.ofVirtual().factory();
for (int i=0; i<100000; i++) {
    Thread vt = tf.newThread(() -> { ... });
    executor.submit(vt);
    // 也可以直接传入Runnable或Callable:
    executor.submit(() -> {
        System.out.println("Start virtual thread...");
        Thread.sleep(1000);
        System.out.println("End virtual thread.");
        return true;
    });
}
```
# 三，总结
通过以上部分，我们了解了:
1, Java的背景知识，例如Java语言的特点和版本
2, 简介了Java是怎么运行的（JDK/JRE的概念，常见命令行和IDE介绍）
3, Java的用法：
    1）数据类型
    2）类（方法重载，继承和多态，抽象类和接口，静态字段和方法，内部类）
    3）常用核心类（String，自动装箱/拆箱，JavaBean/POJO，enum）
    4）异常（分类，简述jvm处理异常链路，代码捕获异常原则）
    5）反射（介绍，使用场景，用法）
    6）泛型（好处，用法）
    7）常见集合
    8）IO(同步和异步IO，IO模型介绍)
    9）函数式编程（介绍和用法）
    10）单元测试（简介Junit）
    11）多线程（线程状态，常见用法，虚拟线程介绍）

如果你想对Java语言有更深入的了解，还需要了解下JVM的工作原理。如果你是Android开发，还需要去了解下Android系统（Android虚拟机不同于JVM，是执行dex字节码的，同时Android系统有自己单独的分层）。
