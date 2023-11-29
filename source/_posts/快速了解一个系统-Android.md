---
title: 快速了解一个系统-Android
date: 2023-5-15 01:30:25
tags: android
---

> 引言：本文帮助你快速建议对Android系统的认知，不涉及过多使用细节。
<!--more-->
# 一，Android历史与现状
Android是Google主导的基于Linux核心与其他开源软件的一个系统，主要面对移动设备的ARM架构。从2003年开始到23年，已经无数个版本，目前最新的是Android14版本。
该操作系统的核心为Android开源项目，简称“AOSP”, 是根据Apache许可证授权的免费开源软件。是可以自己用Repo下载下来自行编译学习的https://source.android.com/docs/setup/download/downloading?hl=zh-cn
在Google I/O 2019中，Google 宣布，Kotlin 编程语言现在是 Android 应用程序开发人员的首选语言。在此之前，Java一直是Android的首选语言。Andorid的虚拟机也是JVM的一种变种。下面按照Java举例说明。

# 二，系统概述
## 2.1 五层架构
Android系统分为应用层，framework层，Native库层，HAL层和Kernel层。
应用层和framework层一般来说是Java语言编写，Native库一般是c++/c编写，HAL层和Kernel层可以当做一体来看，都是跟硬件打交道的。
之所以搞了个HAL，是因为linux遵守的GPL协议要求所有驱动都要开源，为了规避这个问题，搞了个HAL层。
我们日常的Android应用开发只是庞大体系的冰山一小角。消息体系，进程调度通信等等都是Framework层和Native层来做的。四大组件只是程序的片段。
如果你是底层库或驱动开发，则需要把AOSP下下来，然后编写自己的库或者驱动代码，重新make交叉编译，然后烧录/OTA升级到产品中。（AOSP中的make树非常庞杂）。
如果你是应用层开发开发，则只需要开发自己的App，混淆签名上架到应用商店即可。
![](/img/17008155615845.jpg)

## 2.2 Android编译相关
### 1）Andorid虚拟机
Android 虚拟机前后共有两套：
- Dalvik:Android 在4.4 版本上同时提供了Dalvik与ART, 但是Dalvik是作为默认执行环境的，我们的源代码最终会编译为.dex文件，然后运行在Dalvik上，.dex 表示 Dalvik EXecutable文，Dalvik 的原理可以类比JVM。 Android5.0 以后就被完全废弃了。

- ART 其是Android Runtim 的缩写。 Android 从5.0以后就完全使用了新的虚拟机ART，其与Dalvik有很大的区别。其推出的目的主要是为了提高Android的执行效率，减少卡顿现象。那它是怎么做的呢？ART 采用了一种叫AOT (ahead of time) 来代替目前的在 runtime 时的 Interpreter 与 JIT。ART 不是等到App运行的时候才去运行dex文件，而是在App安装的时候就通过 AOT编译器 将.dex文件编译为对应的.oat二进制文件，当用户点击App的启动图标时，ART直接加载.oat文件去执行。其中那个.oat文件就是一个ELF文件，其是当前机器的可执行的文件了。

从前面的分析可以看出，ART 通过安装时将.dex预编译为机器的可执行文件，省去Dalvik在运行时才解释或者即时编译的过程而提高执行效率。

实际上现在的D8/R8虚拟机，Google的工程师将Interpreter,JIT与AOT 三种技术相结合来优化这个过程。
1. 当你首次安装并一个App的时候，AOT不将.dex文件编译为.oat文件，这一步减少了安装时间。系统通过Interpreter的方式来启动App.
2. 当在App运行过程中探测到了热点代码"hot code"，就使用JIT编译
3. 这些通过JIT编译的平台代码及编译配置文件都会被存储在缓存中，加快下次访问的速度
4. 当设备处于空闲时间时，AOT 编译器就会启动，结合编译配置文件将热点代码编译为.oat可执行文件
5. 当再次运行App时，ART就可以直接运行.oat文件了
![](/img/17008178464945.jpg)



### 2）Andorid编译打包流程
![](/img/17008179982601.jpg)

1. 通过AAPT(Android Assets Packing Tool) 编译资源文件，将资源文件打包编译并生成生成R.java文件，就是放各种资源Id的那个文件。
2. 通过Java编译器javac将.java 源代码文件编译为.class字节码文件
3. 通过Dalvik 编译器 将.class文件转化为.dex文件
4. 通过Apk builder 将打包后的资源与.dex文件一起生成APK文件。

以上流程不涉及混淆签名。

# 三，进程和进程间通信
## 3.1 进程
  一般来说，每个App运行在一个单独的进程中，也就是一个单独的Andorid虚拟机。应用开发中使用多进程只有一种方法，那就是给四大组件（Activity、Service、Receiver、ContentProvider）在AndroidMenifest中指定android:process属性。除以以外无法指定进程。（极其特殊的情况下，可以通过JNI在native层来fork进程，但是这个方法非常少见）。多进程模式下，不同进程的组件拥有独立的虚拟机，Application和内存空间。

   两个应用也可以通过ShareUID跑在同一个进程。这要求两个应用有相同的ShareUID并且签名相同，且每个应用是全局进程（进程名不是:开头）。我们可以用过adb shell ps| grep packagename查看进程信息。
   
   多进程模式下，我们不得不面对的问题，就是跨进程通信。Android是基于Linux内核的，所以Linux的内存通信方式（共享内存，pipe，Socket等）Android都是支持的。但是Andorid有一套自己独有的进程间通信方式Binder，Binder也是Android四大组件进程间通信的底层技术基础。
## 3.2 进程间通信
  Andorid开发中常见的跨进程应用方式，如Intent拉起非同进程的activity/service/broadcast receiver，Messenger，AIDL, Socket, 共享文件和SharePreferences。
  其中Intent，Messenger, AIDL内部都是基于Binder的，Andorid Framework中各种系统服务进程之间大部分也都是通过Binder通信的。
  下面我们先讲讲Binder。
### 1）Binder原理和实现
**我们先来讲讲Binder是什么**：
* 通常意义下，Binder指的是一种通信机制；我们说AIDL使用Binder进行通信，指的就是Binder这种IPC机制。
* 对于Server进程来说，Binder指的是Binder本地对象
* 对于Client来说，Binder指的是Binder代理对象，它只是Binder本地对象的一个远程代理；对这个Binder代理对象的操作，会通过驱动最终转发到Binder本地对象上去完成；对于一个拥有Binder对象的使用者而言，它无须关心这是一个Binder代理对象还是Binder本地对象；对于代理对象的操作和对本地对象的操作对它来说没有区别。
* 对于传输过程而言，Binder是可以进行跨进程传递的对象；Binder驱动会对具有跨进程传递能力的对象做特殊处理：自动完成代理对象和本地对象的转换。

**接下来我们讲讲Binder的原理和优势：**
   首先我们明确下几个概念：操作系统的虚拟存储空间是划分为用户控件和内核空间的，两个空间之间的交互需要借助系统调用，系统调用是占用一定开销的。我们的应用进程都是用户态的进程，Linux传统IPC都是需要借助内核空间来完成不同用户进程间的通信的。
    那Binder是什么原理呢？
    Binder可以理解为基于共享内存的定制化扩展，Binder借助了两个技术： Linux 的动态内核可加载模块LKM和内存映射mmp。
    因为Binder并不是Linux内核的一部分，所以它借助LKM把一个虚拟设备：Binder驱动（/dev/binder）动态添加到内核空间中，作为用户进程之间通信的专用内核模块。Binder 驱动就如同路由器一样，是整个通信的核心；驱动负责进程之间 Binder 通信的建立，Binder 在进程之间的传递，Binder 引用计数管理，数据包在进程之间的传递和交互等一系列底层支持。
    同时Binder机制中利用mmp技术将内核中数据接收缓存区和接收进程做了空间映射，减少数据的拷贝次数，用内存读写取代I/O读写，提高文件读取效率。Binder一次跨进程通讯只涉及一次系统调用。
    一次完整的 Binder IPC 通信过程通常是这样：
1. 首先 Binder 驱动在内核空间创建一个数据接收缓存区；
2. 接着在内核空间开辟一块内核缓存区，建立内核缓存区和内核中数据接收缓存区之间的映射关系，以及内核中数据接收缓存区和接收进程用户空间地址的映射关系；
3. 发送方进程通过系统调用将数据 copy 到内核中的内核缓存区，由于内核缓存区和接收进程的用户空间存在内存映射，因此也就相当于把数据发送到了接收进程的用户空间，这样便完成了一次进程间的通信。
    ![](/img/17011358572085.jpg)
   讲完了Binder原理，就该讲**Binder的设计实现**：
      Binder 是基于 C/S 架构的。由一系列的组件组成，包括 Client、Server、ServiceManager、Binder 驱动。如果那网络模型比喻的话，就好比客户端，服务端，DNS服务器，路由器之间的关系。因为我们代码中具体的Binder都是用名字（类名）来寻找的，ServiceManager的作用就是将字符形式的 Binder 名字转化成 Client 中对该 Binder 的引用，使得 Client 能够通过 Binder 的名字获得对 Binder 实体的引用。ServiceManager和其他进程同样通过Binder交互，只不过它的引用是固定的0。 每个Server通过驱动向 ServiceManager 中注册 Binder。
      还有就是进程间传递的只能是基本类型数据，那进程间方法调用是怎么实现的呢？
      简单来说，Client进程只不过是持有了Server端的代理；代理对象协助驱动完成了跨进程通信。也就是说Client进程的操作其实是对于影子的操作，影子利用Binder驱动最终让真身完成操作。
   
  ![](/img/17011361950255.jpg)
  Binder和实现清楚了之后，我们来思考一个问题，**为什么要单独搞一个Binder出来呢？** 
  主要有两点，**性能和安全**。在移动设备上，广泛地使用跨进程通信肯定对通信机制本身提出了严格的要求；Binder相对出传统的Socket方式，更加高效；另外，传统的进程通信方式对于通信双方的身份并没有做出严格的验证，只有在上层协议上进行架设；比如Socket通信ip地址是客户端手动填入的，都可以进行伪造；而Binder机制从协议本身就支持对通信双方做身份校检（Service Manager的存在），因而大大提升了安全性。这个也是Android权限模型的基础。
### 2）Java层Binder的使用(AIDL)
  应用层开发中，我们需要自己手写一个自定义场景的跨进程通信的程序，更多是通过AIDL的方式，及基于他的Messager。
  **什么是AIDL呢？**
  通俗来讲，就是Andorid为了我们更好的实现Binder中client和service，帮我们做了一些模型封装，暴露一些实现方法和接口，让我们可以更专注的写业务逻辑。
  怎么实现一个AIDL呢?
  首先我们要明确，能够跨进程传输的都得是可以序列化的对象（如果是一个类，那么类中的每个成员都必须是可以序列化的）。Andorid中为我们提供了两个序列化接口，分别是Serializable和Parcelable，一个类必须继承自这两个接口，这个类的对象才可以序列化成功。那两者的区别是什么呢？
  * 前者是Java提供的接口，后者是Android独有的，是官方推荐的序列化方式。
  * 前者使用简单，后者序列化效率高
  * 一般来说首选后者，尤其是频繁序列化的场景。
  * 系统已经为我们提供了许多实现Parcelable的类，如Intent, Bundle, Bitmap等
  其次 **AIDL编程的流程是什么呢** （以下是Activity和Service通信的例子）？
1. 编写一个AIDL接口(ICompute.aidl)，里面有希望跨进程调用的接口和成员。
2. 用编译工具编译之后，可以得到对应的java接口(ICompute.java)。这个java接口继承自IInterface，内部有一个抽象内部类Stub。Stub中有ICompute接口的具体实现，同时内部有一个静态Proxy类（继承自ICompute和IBinder）。Proxy内部也有ICompute接口的具体实现，只不过这个实现是将数据序列化/反序列化加工后传递给Stub中的onTransact来完成调用，onTransact最终会调用Stub中具体实现接口。
3. 在Activity中bindService中的ServiceConnection类参数中，在onServiceConnected回调中通过ICompute.aidl.Stub.asInterface获取ICompute引用，并通过该引用完成跨进程调用。实际开发中我们需要自己实现的逻辑就是Stub.Proxy类的调用链路，具体来说就是写一个自己的后台实现类（继承自Stub）。

  结合之前的原理讲解，我们可以看出来，Stub就是server端的接口实现类，Stub.Proxy就是server进程的Binder对象的本地代理，也就是clinet进程最终调用的对象（跨进程调用中Stub.asInterface返回的对象就是Stub.Proxy）。
  注意：AIDL同时兼容了普通的同一进程场景系的c-s模式，也就是Stub.asInterface中会采用策略模式，根据是否是跨进程通讯场景，决定是返回Stub.Proxy还是Stub类本身。
  ![](/img/17011466497843.jpg)
  **展开讲讲Stub和Stub.Proxy中参数和接口**
  * DESCRIPTOR：Binder的唯一标识，一般用当前Binder的类名表示。也是ServerManager解析的名字。
  * asInterface(android.os.IBinder obj)：用于将服务端的Binder对象转换成客户端所需的AIDL接口类型的对象，内部是根据是否是同一进程进行策略转换。
  * asBinder：返回当前Binder对象
  * onTransact：这个方法运行在服务端中的Binder线程池中，当客户端发起跨进程请求时，远程请求会通过系统底层封装后交由此方法来处理。方法内部通过code标志位区分目标方法是什么。需要注意的是，如果此方法返回false，那么客户端的请求会失败，因此我们可以利用这个特性来做权限验证，毕竟我们也不希望随便一个进程都能远程调用我们的服务。
  * Proxy#xxx：client调用server方法的入口方法，也是我们实现具体业务逻辑的入口。这个方法会调用transact方法，发起真正的跨进程调用，并阻塞等待结果。
  
  具体开发中还有一些注意事项：
*   异常情况的处理：当server端进程异常终止的时候，Binder会死亡，这个时候client端需要监听来避免影响到具体功能。这个监听是通过Binder的linkToDeath和unlinkToDeath来完成的。
*   因为跨进程方法是阻塞的，耗时的，所以不应该在UI线程发起请求。
*   因为服务端Binder其实是运行在Binder线程池中，所以服务端的对应接口实现应该按照同步的方式来实现。 
*   如果我们的项目规模特别大，内部多个业务模块都涉及AIDL调用，则为了避免开辟多个Server进程处理，可以汇总到一个Server进程。中间自己写一个Binder连接池进行分发。
![](/img/17011511678426.jpg)


### 3）Android应用层其他进程间通信方式
除了AIDL。Andorid中还有其他一些IPC方式：
1. 共享文件适合场景：对数据同步要求不高，要自己处理好并发读/写的问题。
2. SharePreferences：虽然内部也对应着/data/data/package name/shared_prefs下的一个XML文件，但是由于由于系统对它的读/写有一定的缓存策略，所以在多进程模式下，系统对它的读/写就变得不可靠，当面对高并发的读/写访问，有很大几率会丢失数据，因此，不建议在进程间通信中使用SharedPreferences。
3. Messenger：AIDL的进一步封装，一种消息型轻量IPC调用。c-s间传递的是Message，server端是串行处理消息的。适合场景：跨进程消息传递，且并发量不高。
4. ContentProvider：底层实现依然是Binder。适合传递表格形式组织的数据，通常是数据库（SQLite），但其实并可以传递图片，视频等文件。配置文件中静态注册，并指定唯一authorities和Permission。CRUD操作是在Binder线程池中被调用的，所以内部要做好线程同步（数据库的封装接口内部一般是有同步处理的，这种情况下可以不自己处理）。要观察一个ContentProvider中的数据改变情况，可以通过ContentResolver的registerContentObserver方法来注册观察者，通过unregisterContentObserver方法来解除观察者。  

# 四，线程
讲完了进程，该讲讲Android的线程。
  Android的线程分为主线程和非主线程。其中操作UI只能在主线程进行，主线程默认开启Message Loop；子线程中如果需要可以自己手动创建Message Loop，子线程中处理一些耗时的后台业务，如果需要更新子线程的话，则需要getMainLoop向主线程Loop发消息，抛到主线程处理。
  Andoird的线程用法除了Thread类外，还有一些更方便的封装：AsyncTask、IntentService以及HandlerThread。但是他们内部实现仍然是传统的线程和线程池。
*   AsyncTask封装了线程池和Handler，它主要是为了方便开发者在子线程中更新UI
*   HandlerThread是一种具有消息循环的线程，在它的内部可以使用Handler
*   IntentService是一个服务，系统对其进行了封装使其可以更方便地执行后台任务，IntentService内部采用HandlerThread来执行任务，当任务执行完毕后IntentService会自动退出。
*   ThreadPool：FixedThreadPool(只有不会被回收的核心线程)、CachedThreadPool(只有无上限的非核心线程)、ScheduledThreadPool(固定数目核心线程+不固定数目非核心线程)以及SingleThreadExecutor。直接或间接地通过配置ThreadPoolExecutor来实现自己的功能特性。
    ThreadPoolExecutor执行任务时大致遵循如下规则：
1. 如果线程池中的线程数量未达到核心线程的数量，那么会直接启动一个核心线程来执行任务。
2. 如果线程池中的线程数量已经达到或者超过核心线程的数量，那么任务会被插入到任务队列中排队等待执行。
3. 如果在步骤2中无法将任务插入到任务队列中，这往往是由于任务队列已满，这个时候如果线程数量未达到线程池规定的最大值，那么会立刻启动一个非核心线程来执行任务。
4. 如果步骤3中线程数量已经达到线程池规定的最大值，那么就拒绝执行此任务，ThreadPoolExecutor会调用RejectedExecutionHandler的rejectedExecution方法来通知调用者。
   
这里具体用法接口不展开，读者可以去官方查询。

# 五，Android应用启动过程
## 5.1 init进程启动
  Android系统实际上是运行在Linux内核之上的一系列“服务进程”。其中第一个会被启动的进程，就是PID为0的init进程。这个进程通过init.rc脚本来构建初始运行形态的，也就是启动一系列系统服务进程。其中最重要的几个服务就是ServiceManager, Zygote和SystemServer。
  * ServiceManager上文已经介绍过，相当于Binder机制中的DNS服务器。
  * Zygote会启动应用进程，启动主线程，开启主线程消息循环，最终经过一系列调用指定入口Activity的onCreate方法。
  * SystemServer开启由Java语言编写的各种系统服务，比如AMS,PMS,WMS等
  
## 5.2 Activity生命周期
入口Activity进下来进入自己的生命周期：
  ![](/img/17011579928680.jpg)
  
注意：两个活动切换的时候，旧活动的onPause执行完，新活动的OnResume才会执行。所以onPause不能执行重量级操作，尽量放到onStop中。
 
几种异常情况下的生命周期：
1）**资源相关的系统配置发生改变导致Activity被杀死并重新创建**
   例如，旋转屏幕，这种情况Activity即将被销毁并且有机会重新显示。则系统会调用onSaveInstanceState和onRestoreInstanceState来存储和恢复数据。
   时序上的保证是，onSaveInstanceState的时机先于onStop，onRestoreInstanceState后于onStart。
   ![](/img/17011601613536.jpg)

   具体来说，系统会默认为我们保存当前Activity的视图结构，比如文本框中用户输入的数据、ListView滚动的位置等，这些View相关的状态系统都能够默认为我们恢复。
   具体针对某一个特定的View系统能为我们恢复哪些数据，我们可以查看View的源码，和Activity一样，每个View都有onSaveInstanceState和onRestoreInstanceState这两个方法。
   关于保存和恢复View层次结构，系统的工作流程是这样的：首先Activity被意外终止时，Activity会调用onSaveInstanceState去保存数据，然后Activity会委托Window去保存数据，接着Window再委托它上面的顶级容器去保存数据。顶层容器是一个ViewGroup，一般来说它很可能是DecorView。最后顶层容器再去一一通知它的子元素来保存数据，这样整个数据保存过程就完成了。数据恢复过程也是基于类似的委托思想。
    如果不想重建，则指定Activity的configChanges属性，如此Activity就不会重建了，只会调用onConfigurationChanged回调。
    
2）**资源内存不足导致低优先级的Activity被杀死**
   每个进程都有自己的优先级，当系统内存不足时，系统就会按照上述优先级去杀死对应进程。如果杀死的是Activity所在进程，则会在后续通过onSaveInstanceState和onRestoreInstanceState来存储和恢复数数据。
   **常见的进程重要顺序**是：
   1. 前台进程：
      * 它正在用户的互动屏幕上运行一个 Activity（其 onResume() 方法已被调用）。
      * 它有一个 BroadcastReceiver 目前正在运行（其 BroadcastReceiver.onReceive() 方法正在执行）。
      * 它有一个 Service 目前正在执行其某个回调（Service.onCreate()、Service.onStart() 或 Service.onDestroy()）中的代码。
   2. 可见进程：
      * 它正在运行的 Activity 在屏幕上对用户可见，但不在前台（其 onPause() 方法已被调用））
      * 它有一个 Service 正在通过 Service.startForeground()（要求系统将该服务视为用户知晓或基本上对用户可见的服务）作为前台服务运行。
      * 系统正在使用其托管的服务实现用户知晓的特定功能，例如动态壁纸、输入法服务等
   3. 服务流程：包含一个已使用 startService() 方法启动的 Service。已经运行了很长时间（例如 30 分钟或更长时间）的服务的重要性可能会降位到下文LRU缓存列表中。    
   4. 缓存进程：通常包含用户当前不可见的一个或多个 Activity 实例（onStop() 方法已被调用并返回）。这些进程保存在伪 LRU 列表中。

   注意：如果一个进程中没有四大组件在执行，那么这个进程将很快被系统杀死，因此，一些后台工作不适合脱离四大组件而独自运行在后台中，这样进程很容易被杀死，比较好的方法是放到Service中。

## 5.3 Activity启动模式
  任务是用户在执行某项工作时与之互动的一系列 Activity 的集合。这些 Activity 按照每个 Activity 打开的顺序排列在一个返回堆栈中。Activity启动模式决定了活动在返回堆栈中的存储方式。目前有四种启动模式：standard、singleTop、singleTask和singleInstance。
### 1）启动模式种类
1. standard：标准模式，这也是系统的默认模式。每次启动一个Activity都会重新创建一个新的实例，不管这个实例是否已经存在。谁启动了这个Activity，那么这个Activity就运行在启动它的那个Activity所在的栈中。
2. singleTop：栈顶复用模式。在这种模式下，如果新Activity已经位于任务栈的栈顶，那么此Activity不会被重新创建，同时它的onNewIntent方法会被回调，通过此方法的参数我们可以取出当前请求的信息。如果新Activity的实例已存在但不是位于栈顶，那么新Activity仍然会重新重建。
3. singleTask：栈内复用模式。这是一种单实例模式，在这种模式下，只要Activity在一个栈中存在，那么多次启动此Activity都不会重新创建实例，和singleTop一样，系统也会回调其onNewIntent。singleTask默认具有clearTop的效果。
4. singleInstance：单实例模式。这是一种加强的singleTask模式，它除了具有singleTask模式的所有特性外，还加强了一点，那就是具有此种模式的Activity只能单独地位于一个任务栈中。由于栈内复用的特性，后续的请求均不会创建新的Activity，除非这个独特的任务栈被系统销毁了。

一般来说，系统中的任务栈分为前台任务栈和后台任务栈，用户可以通过切换将后台任务栈再次调到前台。
   我们也可以给Activity指定任务栈的名字（用TaskAffinity属性指定，默认是包名，这里任务栈名称和前后台任务栈是两个维度的东西）。TaskAffinity属性主要和singleTask启动模式或者allowTaskReparenting属性配对使用，可知指定待启动或者启动活动的栈，在其他情况下没有意义。

### 2）指定启动模式
有两种方法，第一种是通过AndroidMenifest为Activity指定启动模式launchMode，另一种情况是通过在Intent中设置标志位来为Activity指定启动模式addFlags。
两者有些区别:
* 第二种方式的优先级要高于第一种;
* 上述两种方式在限定范围上有所不同
   
# 六，四大组件
## 6.1 四大组件概述
  **从注册方式来说**，Android的四大组件中除了BroadcastReceiver以外，其他三种组件都必须在Android-Manifest中注册，对于BroadcastReceiver来说，它既可以在AndroidManifest中注册也可以通过代码来注册。
  **从调用方式来说**，Activity、Service和BroadcastReceiver需要借助Intent，而ContentProvider则无须借助Intent。
  **从功能来说**，
  Activity是一种展示型组件，用于向用户直接地展示一个界面，并且可以接收用户的输入信息从而进行交互。Activity的生命周期和launchMode上面都有介绍。Activity是可以通知的，可以通过调用Activity的finish方法停止运行。
  Service是一种计算型组件，用于在后台执行一系列计算任务。由于Service组件工作在后台，因此用户无法直接感知到它的存在。Service组件有两种状态：启动状态和绑定状态。其中绑定状态方便和前台Activity进行通信。Service组件也是可以停止的，停止一个Service组件稍显复杂，需要灵活采用stopService和unBindService这两个方法才能完全停止一个Service组件。
  BroadcastReceiver是一种消息型组件，用于在不同的组件乃至不同的应用之间传递消息。BroadcastReceiver也叫广播，广播的注册有两种方式：静态注册和动态注册。广播又分为有序和无序广播。BroadcastReceiver组件一般来说不需要停止，它也没有停止的概念。
  ContentProvider是一种数据共享型组件，用于向其他组件乃至其他应用共享数据。另外ContentProvider组件也不需要手动停止。
## 6.2 四大组件用法详述
  以上，我们对四大组件的功能有了一定的了解。Activity在前面已经有了详细的介绍，下面我们展开介绍下其他三个组件：
**  1）Service**
  服务是一个后台运行的组件，执行长时间运行且不需要用户交互的任务。即使应用被销毁也依然可以工作。服务基本上包含两种状态：启动状态和绑定状态。两种状态是可以共存的。
  ![](/img/17011647433959.jpg)

  两种状态下的生命周期模型是不一样的，左图是当服务通过startService()被创建时的生命周期，右图则显示了当服务通过bindService()被创建时的生命周期：
  ![](/img/17011646183097.jpg)
继承自Service基类，需要实现的回调如下：

 ![](/img/17011647851034.jpg)
 
 ** 2）BroadcastReceiver**
  广播接收器用于响应来自其他应用程序或者系统的广播消息。收到后可以根据广播内容执行自己的逻辑或者拦截。在实际开发中通过Context的一系列send方法(sendBroadcast等)来发送广播，被发送的广播会被系统发送给感兴趣的广播接收者，发送和接收过程的匹配是通过广播接收者的<intent-filter>来描述的。BroadcastReceiver组件可以用来实现低耦合的观察者模式，它不适合用来执行耗时操作。
  BroadcastReceiver的执行步骤是：
  1. 创建广播接收器。广播接收器需要实现为BroadcastReceiver类的子类，并重写onReceive()方法来接收以Intent对象为参数的消息。
  2. 注册广播接收器。可以在AndroidManifest.xml中静态注册广播接收器来监听制定的广播意图，静态注册即使应用没启动也能接收到广播，android 8.0 及以后，google对静态注册加了限制，发送广播时，需要指定接收的类；也可以在代码中通过Context.registerReceiver()来实现动态注册，并且在不需要的时候要通过Context.unRegisterReceiver()来解除广播，此种形态的广播必须要应用启动才能注册并接收广播。
  
**  3）ContentProvider**
  ContentProvider组件通过请求从一个应用程序向其他的应用程序提供数据。这些请求由类 ContentResolver的方法来处理。内容提供者可以使用不同的方式来存储数据。数据可以被存放在数据库，文件，甚至是网络。内容提供者的行为和数据库很像。你可以查询，编辑它的内容，使用 insert()， update()， delete() 和 query() 来添加或者删除内容。多数情况下数据被存储在 SQLite 数据库。
    
![](/img/17011654534738.jpg)

   创建自己的ContentProvider需要：
1. 首先，你需要继承类 ContentProviderbase 来创建一个ContentProvider类。
2. 其次，你需要定义用于访问内容的你的内容提供者URI地址。
3. 接下来，你需要创建数据库来保存内容。通常，Android 使用 SQLite 数据库，并在框架中重写 onCreate() 方法来使用 SQLiteOpenHelper 的方法创建或者打开提供者的数据库。当你的应用程序被启动，它的每个内容提供者的 onCreate() 方法将在应用程序主线程中被调用。
4. 最后，使用<provider.../>标签在 AndroidManifest.xml 中注册内容提供者。

## 6.3 AMS和PMS
   这里为什么要提AMS和PMS呢？是因为AMS（ActivityManagerService）主要负责系统中四大组件的启动、切换、调度及应用进程的管理和调度等工作。也就是自大组件的功能实现都在AMS里。
   我们提到的AMS, 准确说是一个AMS家族，其内部也是一个跨进程的cs模型。入口调用是ActivityManager，内部真正的实现在ActivityManagerService中。
   
![](/img/17011716470418.jpg)
**那PMS又是什么呢？**
  上面我们已经知道了AMS的功能，但是AMS是怎么知道应用都有哪些四大组件，他们的属性都是什么呢？这就引出了PMS。
  PMS（PackageManagerService）是 Android 提供的包管理系统服务，它用来管理所有的包信息，包括应用安装、卸载、更新以及解析 AndroidManifest.xml。
  你是否有考虑过为什么我们手机开启启动时会很慢？这是因为 在手机启动时 PMS 会在这段时间处理 apk 解析，至少有 70% 的启动时间耗费在 PMS 解析上，所以这也是为什么手机开机启动比较慢的原因之一。跟AMS相关的主要是解析功能，可以理解为 PMS 保存了后续提供给 AMS 所需要的数据，它是具有保存应用数据的缓存。
  PMS的处理流程简单理解就是手机开机时会去扫描两个目录 /data/app 和 /system/app，去解析这两个目录的 apk 文件的 AndroidManifest.xml 生成应用的摘要信息保存为 Java Bean 到内存。AMS和PMS的通信也是通过Binder。

**AMS具体做了什么呢？**
  这个过程比较长，简单来说就是：
1. 调用 startActivity() 时，实际会走到 Instrumentation，由它与 AMS 通信
2. Instrumentation 会找 ServiceManager 获取 AMS（实际是获取 binder 代理）调用 startActivity()。在 9.0 之前是获取 AMS，9.0 之后是获取 ATMS
3. AMS 找到 PMS 获取启动的 Activity 信息
4. 然后判断需要启动的 Activity 所在进程是否已存在，不存在 AMS 通过 socket 通知 Zygote fork 进程，然后反射调用 ActivityThread 的 main()，创建 Instrumentation、Application 和 Activity 以及走生命周期流程，如果需要启动的 Activity 所在进程已经存在，走生命周期流程。每个应用都有自己的ApplicationThread进行生命周期管理，每个进程都有自己的ActivityThread进行生命周期管理。
![](/img/17011735638655.jpg)

# 七，Andorid的安全性
Android的安全措施主要有以下几个方面：
* 通过 Linux 内核在操作系统级别提供的强大安全功能
* 强制所有应用使用应用沙盒
* 安全的进程间通信
* 应用签名
* 应用定义的权限和用户授予的权限

我们展开来讲讲：
**1，Linux安全**
Android平台的基础是Linux内核。Linux 内核为 Android 提供了一些关键的安全功能，其中包括：
* 基于用户的权限模式
* 进程隔离
* 用于实现安全 IPC 的可扩展机制
* 能够移除内核中不必要的和可能不安全的部分

**2，应用沙盒**
Android 会为每个 Android 应用分配一个独一无二的用户 ID (UID)，并让应用在自己的进程中运行。Android 会使用该 UID 设置一个内核级应用沙盒。内核会在进程级别利用标准的 Linux 机制（例如，分配给应用的用户 ID 和组 ID）实现应用和系统之间的安全防护。由于应用沙盒位于内核层面，因此该安全模型的保护范围扩展到了原生代码和操作系统应用。位于更高层面的所有软件（例如，操作系统库、应用框架、应用运行时环境和所有应用）都会在应用沙盒中运行。
    那不同应用间怎么共享数据呢？Andorid系统推荐以下两种方式：
* ContentProvider,用于应用间共享文件。
* MediaStore，用于应用间共享媒体文件（仅限照片、视频和音频文件）。

**3，安全的IPC**
这里Binder的安全性上文已经有过讲解，Android官方的推荐IPC方式是binder，server,intent和ContentProvider。
**4，应用签名**
通过应用签名，开发者可以标识应用创作者并更新其应用，而无需创建复杂的接口和权限。
在 Android 上，应用签名是将应用放入其应用沙盒的第一步。Android系统根据签名的不同，给不同的应用分配不同的UID。如果两个应用的签名相同，则会被分配一样的UID，共享一个应用沙盒。   
  应用可以由第三方（原始设备制造商 (OEM)、运营商、其他应用市场）签名，也可以自行签名。Android 提供了使用自签名证书进行代码签名的功能，而开发者无需外部协助或许可即可生成自签名证书。应用并非必须由核心机构签名。Android 目前不对应用证书进行 CA 认证。
  Google Play 或 Android 设备上的软件包安装程序会拒绝没有获得签名就尝试安装的应用。

**5，应用定义的权限和用户授予的权限**
应用权限有助于保护受限数据和受限操作，从而为保护用户隐私。
有些权限是用户安装应用时自动授予的权限，称为**安装时权限**。其他权限则需要应用在运行时进一步请求权限，此类权限称为**运行时权限**。每项权限的保护级别取决于其类型，具体参考https://developer.android.com/reference/android/Manifest.permission。
   
1）安装时权限
安装时权限授予应用对受限数据的受限访问权限，或允许应用执行对系统或其他应用只有最低影响的受限操作。包括**一般权限和签名权限**
* 一般权限：对用户隐私和其他应用的运行构成的风险很小权限，normal 保护级别，如MANAGE_EXTERNAL_STORAGE。
* 签名权限：只有当应用与定义权限的应用或 OS 使用相同的证书签名时，系统才会向应用授予签名权限。signature 保护级别。

2）运行时权限
运行时权限也称为**危险权限**，此类权限授予应用对受限数据的额外访问权限，或允许应用执行对系统和其他应用具有更严重影响的受限操作。dangerous 保护级别。如麦克风和摄像头，位置信息等。
![](/img/17012410783602.jpg)


上述流程中系统权限对话框中的文本会提及与您请求的权限关联的**权限组**，如果同意就直接获得权限组中所有的权限。系统权限组的作用只是在应用请求密切相关的多个权限时，帮助系统尽可能减少向用户显示的系统对话框数量。但是注意权限的划归不同版本是有变化的，您的应用不应依赖于特定权限组之内或之外的权限。

# 七，消息机制
就像所有的事件驱动模型一样，Android的消息机制是每个线程一个自己的消息队列，基于生产者消费者模型投入消息和拿出处理消息。**主要应用场景是解决在子线程中进行UI操作的问题**。
   首先，**为什么不能在子线程访问UI?** 因为Andorid的UI线程是不安全的，多线程并发访问会带来不可预期的状态。这种情况下如果加锁，会降低效率，增大开销。最简单高效的方法就是采用单线程模型处理UI。子线程通过消息发送到主线程，最终在主线程访问UI。基本所有的界面交互系统都是采用这个思路，比如ios，unity等。    
   具体来说，Android的消息机制主要是指Handler的运行机制，Handler的运行需要底层的MessageQueue和Looper的支撑。流程上，就是Handler发送消息到MessageQueue中存储，Looper从MessageQueue中取出消息并交给Handler执行。Handler和MessageQueue，Looper是一一对象的关系，每个线程一份自己的。
   其中MessageQueue内部是用单链表的数据结构来存储消息（单链表的优势是插入删除效率较高）。通过Handler的相同接口（getLooper()）在不同线程获取不同Looper的能力是借助ThreadLocal来实现的。
   主线程就是ActivityThread，在ActivityThread的main方法中，新建了Looper并开启了Looper循环。所以主线程中可以直接获得handler并使用。其他的子线程是需要自己创建Looper，获取对应handler并开启Looper循环的。子线程中操作UI是通过获取主线程Handler(mainHandler = new Handler(Looper.getMainLooper()))),然后将消息抛到主线程(mainHandler的send/post方法)执行的。
```
           new Thread("Thread#2") {
            @Override
            public void run() {
                Looper.prepare();
                Handler handler = new Handler();
                Looper.loop();
            };
        }.start();
```

   ![](/img/17011797653287.jpg)
## 7.1 Looper和Handler具体使用
**1）Looper**
   Looper在Android的消息机制中扮演着消息循环的角色，具体来说就是它会不停地从MessageQueue中查看是否有新消息，如果有新消息就会立刻处理，否则就一直阻塞在那里。
   由于主线程的Looper比较特殊，所以Looper提供了一个getMainLooper方法，通过它可以在任何地方获取到主线程的Looper。
   Looper也是可以退出的，Looper提供了quit和quitSafely来退出一个Looper，二者的区别是：quit会直接退出Looper，而quitSafely只是设定一个退出标记，然后把消息队列中的已有消息处理完毕后才安全地退出。Looper退出后，通过Handler发送的消息会失败，这个时候Handler的send方法会返回false。在子线程中，如果手动为其创建了Looper，那么在所有的事情完成以后应该调用quit方法来终止消息循环，否则这个子线程就会一直处于等待的状态，而如果退出Looper以后，这个线程就会立刻终止，因此建议不需要的时候终止Looper。
**2）Handler**
    Handler的工作主要包含消息的发送和接收过程。消息的发送可以通过post的一系列方法以及send的一系列方法来实现，post的一系列方法最终是通过send的一系列方法来实现的。
    Handler发送消息的过程仅仅是向消息队列中插入了一条消息，MessageQueue的next方法就会返回这条消息给Looper, Looper收到消息后就开始处理了，最终消息由Looper交由Handler处理，即Handler的dispatchMessage方法会被调用，这时Handler就进入了处理消息的阶段。 
    Handler的dispatchMessage方法是在创建Handler的线程里执行，也就是说虽然发送和执行逻辑都在同一个handler代码中，但是是在两个线程中被执行的。
    dispatchMessage中消息的优先级处理顺序如下：
1. 首先会去执行message的callback，也就是handler的post方法传递的Runnable参数；
2. 其次如果Handler构造函数中有Handler.Callback类型参数，则执行Handler.Callback中的    handleMessage逻辑；
3. 最后会去执行Handler定义的时候handleMessage里的逻辑。
![](/img/17011899708481.jpg)
    

# 八，View体系和WMS
**首先什么是View？**
   View是Android中所有可视控件的基类，不管是简单的Button和TextView还是复杂的RelativeLayout和ListView，它们的共同基类都是View。所以说，View是一种界面层的控件的一种抽象，它代表了一个控件。除了View，还有ViewGroup，但是ViewGroup也继承自View。这就意味着View本身就可以是单个控件也可以是由多个控件组成的一组控件，通过这种关系就形成了View树的结构，这和Web前端中的DOM树的概念是相似的。
   通过查看View API，我们发现一个View接口中有绘制相关的（onMeasure,onLayout,onDraw等），有Event交互事件相关的，有焦点相关的，和window关系相关的（onAttachedToWindow等）。
   由此我们知道一个View是可以计算绘制渲染的，可交互的，可以指定附在哪个window上的控件。
**其次什么是Window？**
  Window表示一个窗口的概念，Android中所有的视图都是通过Window来呈现的，不管是Activity、Dialog还是Toast，它们的视图实际上都是附加在Window上的，因此Window实际是View的直接管理者。
  单击事件由Window传递给DecorView，然后再由DecorView传递给我们的View，就连Activity的设置视图的方法setContentView在底层也是通过Window来完成的。
   Window是一个抽象类，它的具体实现是PhoneWindow。
**最后什么是WMS？**
  创建一个Window需要通过WindowManager。WindowManager是外界访问Window的入口，Window的具体实现位于WindowManagerService中，WindowManager和Window-ManagerService的交互是一个IPC过程。这就类似AM和AMS的关系。
     
## 8.1 View事件体系
### 1）View的位置参数
   View的位置主要由它的四个顶点来决定，分别对应于View的四个属性：top、left、right、bottom。
需要注意的是，这些坐标都是相对于View的父容器来说的，因此它是一种相对坐标。
### 2）View的滑动
通过三种方式可以实现View的滑动：
* 第一种是通过View本身提供的scrollTo/scrollBy方法来实现滑动；
* 第二种是通过动画给View施加平移效果来实现滑动；
* 第三种是通过改变View的LayoutParams使得View重新布局从而实现滑动。

三者的区别是什么呢？
第一种只能改变View内容的位置，不能改变View在布局的位置。所以不适合交互场景。
第二种主要是操作View的translationX和translationY属性，既可以采用传统的View动画，也可以采用属性动画。如果有交互的话，就采用属性动画，因为View动画会有点击问题（并不能真正改变View位置，只是对其影响操作）。
第三种使用稍微麻烦点，但是同样可以符合预期的交互。

### 3）View的事件分发机制
当一个MotionEvent产生了以后，就是你的手指在屏幕上做一系列动作的时候，系统需要把这一系列的MotionEvent分发给一个具体的View。我们重点需要了解这个分发的过程，那么系统是如何去判断这个事件要给哪个View，也就是说是如何进行分发的呢？
    一个点击事件产生后的传递过程是：Activity->Window->DecorView->View, 从DecorView开始是一种自顶而下的传递方式。    
![](/img/17012240138660.jpg)
那分发和拦截过程中用到的是这三个函数：dispatchTouchEvent，onInterceptTouchEvent，onTouchEvent。他们的关系如下，分别负责分发，拦截，消费。
```
    public boolean dispatchTouchEvent(MotionEvent ev) {
        boolean consume = false;//事件是否被消费
        if (onInterceptTouchEvent(ev)){//调用onInterceptTouchEvent判断是否拦截事件
            consume = onTouchEvent(ev);//如果拦截则调用自身的onTouchEvent方法
        }else{
            consume = child.dispatchTouchEvent(ev);//不拦截调用子View的dispatchTouchEvent方法
        }
        return consume;//返回值表示事件是否被消费，true事件终止，false调用父View的onTouchEvent方法
    }
```
具体来说，就是从Activity开始，自顶而下如果有一个View拦截了（onInterceptTouchEvent），就onTouchEvent自行处理；如果子view的onTouchEvent都返回false，那么事件又会被自下而上的抛给父容器，调用父容器的onTouchEvent，直到Activity的onTouchEvent。
![](/img/17012242345869.jpg)

以上View事件的分发逻辑我们已经讲清楚了，具体传递机制中还有一些小结论：
1. 同一个事件序列是指从手指接触屏幕的那一刻起，到手指离开屏幕的那一刻结束，在这个过程中所产生的一系列事件。（DOWN-MOVE-UP）
2. 正常情况下，一个事件序列只能被一个View拦截且消耗。某个View一旦决定拦截，那么这一个事件序列都只能由它来处理。系统会把同一个事件序列内的其他方法都直接交给它来处理，因此就不用再调用这个View的onInterceptTouchEvent去询问它是否要拦截了。
3. 某个View一旦开始处理事件，如果它不消耗ACTION_DOWN事件（onTouchEvent返回了false），那么同一事件序列中的其他事件都不会再交给它来处理，并且事件将重新交由它的父元素去处理，即父元素的onTouchEvent会被调用。
4. 如果View不消耗除ACTION_DOWN以外的其他事件，那么这个点击事件会消失，此时父元素的onTouchEvent并不会被调用，并且当前View可以持续收到后续的事件，最终这些消失的点击事件会传递给Activity处理。
5. ViewGroup默认不拦截任何事件。(onInterceptTouch默认返回false)
6. View的onTouchEvent默认都会消耗事件（返回true），除非它是不可点击的（clickable和longClickable同时为false）。View的enable属性不影响onTouchEvent的默认返回值。
7. 子View可以通过requestDisallowInterceptTouchEvent方法可以在子元素中干预父元素的事件分发过程，但是ACTION_DOWN事件除外。

### 4）View的滑动冲突
滑动冲突的场景有三类：
1. 外部滑动方向和内部滑动方向不一致
2. 外部滑动方向和内部滑动方向一致
3. 上面两种情况的嵌套
![](/img/17012251571456.jpg)

针对以上三种滑动冲突场景，解决策略也不同：
1. 根据滑动是水平滑动还是竖直滑动来判断到底由谁来拦截事件
2. 比较特殊，它无法根据滑动的角度、距离差以及速度差来做判断，但是这个时候一般都能在业务逻辑上找到突破点
3. 同样还是只能从业务逻辑上找到突破点

但不管哪种解决策略，冲突解决的技术方案都是类似的：
1. 外部拦截法
   所谓外部拦截法是指点击事情都先经过父容器的拦截处理，如果父容器需要此事件就拦截，如果不需要此事件就不拦截，这样就可以解决滑动冲突的问题。外部拦截法需要重写父容器的onInterceptTouchEvent方法，在内部做相应的拦截即可。
2. 内部拦截法
   内部拦截法是指父容器不拦截任何事件，所有的事件都传递给子元素，如果子元素需要此事件就直接消耗掉，否则就交由父容器进行处理，这种方法和Android中的事件分发机制不一致，需要配合requestDisallowInterceptTouchEvent方法才能正常工作，使用起来较外部拦截法稍显复杂。
```
//外部拦截法伪代码
        public boolean onInterceptTouchEvent(MotionEvent event) {
            boolean intercepted = false;
            int x = (int) event.getX();
            int y = (int) event.getY();
            switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN: {
                intercepted = false;
                break;
            }
            case MotionEvent.ACTION_MOVE: {
                if (父容器需要当前点击事件) {
                    intercepted = true;
                } else {
                    intercepted = false;
                }
                break;
            }
            case MotionEvent.ACTION_UP: {
                intercepted = false;
                break;
            }
            default:
                break;
              }
              mLastXIntercept = x;
              mLastYIntercept = y;
              return intercepted;
          }
//内部拦截法伪代码
        public boolean dispatchTouchEvent(MotionEvent event) {
            int x = (int) event.getX();
            int y = (int) event.getY();

            switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN: {
                parent.requestDisallowInterceptTouchEvent(true);
                    break;
                }
                case MotionEvent.ACTION_MOVE: {
                    int deltaX = x - mLastX;
                    int deltaY = y - mLastY;
                    if (父容器需要此类点击事件)) {
                        parent.requestDisallowInterceptTouchEvent(false);
                    }
                    break;
                }
                case MotionEvent.ACTION_UP: {
                    break;
                }
                default:
                    break;
                }

                mLastX = x;
                mLastY = y;
                return super.dispatchTouchEvent(event);
            }
```
   
## 8.2 View工作原理
View是Android在视觉上的呈现，那View最终是怎么在界面上展示出来的呢？上文我们介绍View API的时候提到过View有一块绘制类API，这里都会提到。
### 1）ViewRoot和DecorView
**首先什么是DecorView？**
简单来说就是一个Window中的最顶级View。平时我们写代码setContentView其实只是设置了一个内部部分，ViewStub部分一般放置title。DecorView实际上是个FrameLayout。
![](/img/17012262597659.jpg)
**其次什么是ViewRoot？**
ViewRoot对应于ViewRootImpl类，它是连接WindowManager和DecorView的纽带，View的三大流程均是通过ViewRoot来完成的。在ActivityThread中，当Activity对象被创建完毕后，会将DecorView添加到Window中，同时会创建ViewRootImpl对象，并将ViewRootImpl对象和DecorView建立关联。
   View的绘制流程是从ViewRoot的performTraversals方法开始的，它经过measure、layout和draw三个过程才能最终将一个View绘制出来，其中measure用来测量View的宽和高，layout用来确定View在父容器中的放置位置，而draw则负责将View绘制在屏幕上。
   measure、layout和draw的过程都是自顶而下遍历的，比如measuse就是一层一层往下调用，子控件讲measure的结果回传父类，父类根据子类结果进行自己的测量，如此一层一层往上，最终完成测量。
   measure过程决定了View的宽/高，Measure完成以后，可以通过getMeasuredWidth和getMeasuredHeight方法来获取到View测量后的宽/高；Layout过程决定了View的四个顶点的坐标和实际的View的宽/高，完成以后，可以通过getTop、getBottom、getLeft和getRight来拿到View的四个顶点的位置，并可以通过**getWidth和getHeight**方法来拿到View的最终宽/高。Draw过程则决定了View的显示，只有draw方法完成以后View的内容才能呈现在屏幕上。
   
   ![](/img/17012266391362.jpg)

### 2）View的工作流程三大步
### 2.1）Measure
View的measure过程是三大流程中最复杂的一个。Measure这一步骤的时候，系统会将View的LayoutParams根据父容器所施加的规则转换成对应的MeasureSpec，然后再根据这个measureSpec来测量出View的宽/高。
**关于MeasureSpec**
    MeasureSpec代表一个32位int值，高2位代表SpecMode，低30位代表SpecSize。
SpecMode有三类：
* UNSPECIFIED父容器不对View有任何限制，要多大给多大，这种情况一般用于系统内部，表示一种测量的状态。
* EXACTLY父容器已经检测出View所需要的精确大小，这个时候View的最终大小就是SpecSize所指定的值。它对应于LayoutParams中的match_parent和具体的数值这两种模式。
* AT_MOST父容器指定了一个可用大小即SpecSize, View的大小不能大于这个值，具体是什么值要看不同View的具体实现。它对应于LayoutParams中的wrap_content。

 对于顶级View（即DecorView）和普通View来说，MeasureSpec的转换过程略有不同。对于DecorView，其MeasureSpec由窗口的尺寸和其自身的LayoutParams来共同确定；对于普通View，其MeasureSpec由父容器的MeasureSpec和自身的LayoutParams来共同决定，MeasureSpec一旦确定后，onMeasure中就可以确定View的测量宽/高。普通View的MeasureSpec创建规律如下：
 
 ![](/img/17012276718798.jpg)
**关于获取测量数据**
首先measure函数是final的，真正的测量发生在onMeasure，如果想自定义view，也是需要重写onMeasure。
  其次measure之后得到的测量数据不一定准，layout阶段可能发生改变，所以一个比较好的习惯是在onLayout方法中去获取View的测量宽/高或者最终宽/高。
  最后因为View的measure过程和Activity的生命周期方法不是同步执行的，因此无法保证Activity执行了onCreate、onStart、onResume时某个View已经测量完毕了，如果View还没有测量完毕，那么获得的宽/高就是0。那么怎么解决呢？有以下3种方法：
1. Activity/View#onWindowFocusChanged
   这个回调中View已经初始化完毕了，可以获取正确值了。需要注意的是，onWindowFocusChanged会被调用多次，当Activity的窗口得到焦点和失去焦点时（onResume和onPause）均会被调用一次。
2. view.post(runnable)
   Looper调用此runnable的时候，View也已经初始化好了。可以在runnable里获取view宽高值。
3. ViewTreeObserver
   使用ViewTreeObserver的众多回调可以完成这个功能，比如使用OnGlobalLayoutListener这个接口，当View树的状态发生改变或者View树内部的View的可见性发现改变时，onGlobalLayout方法将被回调，因此这是获取View的宽/高一个很好的时机。需要注意的是，伴随着View树的状态改变等，onGlobalLayout会被调用多次。
   
### 2.2）Layout
流程和measure一样，也是自顶而下的调用onLayout，这里不再赘述
### 2.3）Draw
View绘制过程的传递是通过dispatchDraw来实现的，dispatchDraw会遍历调用所有子元素的draw方法，如此draw事件就一层层地传递了下去。
View的绘制过程遵循如下几步：
1. 绘制背景background.draw(canvas)。
2. 绘制自己（onDraw）
3. 绘制children（dispatchDraw）
4. 绘制装饰（onDrawScrollBars）。

### 3）自定义View
#### 3.1）自定义View的分类
1. 继承View重写onDraw方法
   主要用于实现一些不规则的效果，即这种效果不方便通过布局的组合方式来达到，往往需要静态或者动态地显示一些不规则的图形。很显然这需要通过绘制的方式来实现，即重写onDraw方法。采用这种方式需要自己支持wrap_content，并且padding也需要自己处理。
2. 继承ViewGroup派生特殊的Layout
   主要用于实现自定义的布局。用这种方式稍微复杂一些，需要合适地处理ViewGroup的测量、布局这两个过程，并同时处理子元素的测量和布局过程。
3. 继承特定的View（比如TextView）
   这种方法比较常见，一般是用于扩展某种已有的View的功能。这种方法不需要自己支持wrap_content和padding。
4. 继承特定的ViewGroup（比如LinearLayout）
   这种方法也比较常见。采用这种方法不需要自己处理ViewGroup的测量和布局这两个过程。
   
#### 3.2）自定义View注意事项
1. 尽量不要在View中使用HandIer，没必要
   这是因为View内部本身就提供了post系列的方法，完全可以替代Handler的作用，当然除非你很明确地要使用Handler来发送消息。
2. View中如果有线程或者动画，需要及时停止，参考View#onDetachedFromWindow
   当包含此View的Activity退出或者当前View被remove时，View的onDetachedFromWindow方法会被调用，和此方法对应的是onAttachedToWindow，当包含此View的Activity启动时，View的onAttachedToWindow方法会被调用。同时，当View变得不可见时我们也需要停止线程和动画，如果不及时处理这种问题，有可能会造成内存泄漏。
3. View带有滑动嵌套情形时，需要处理好滑动冲突
## 8.3 Window和WMS
View部分已经介绍过Window和WMS的概念和作用，WMS是实际上View的管理者，View只有绑定到Window上才能显示出来。
  Window有三种类型：
*   应用Window。对应着一个Activity
*   子Window。不能单独存在，它需要附属在特定的父Window之中，比如Dialog
*   系统Window。需要声明权限在能创建的Window，比如Toast和系统状态栏。

  Window是分层的，每个Window都有对应的z-ordered，层级大的会覆盖在层级小的Window的上面，这和HTML中的z-index的概念是完全一致的。在三类Window中，应用Window的层级范围是1～99，子Window的层级范围是1000～1999，系统Window的层级范围是2000～2999，这些层级范围对应着WindowManager.LayoutParams的type参数。
  WindowManager所提供的功能很简单，常用的只有三个方法，即添加View、更新View和删除View。（WindowManager继承了ViewManager）。由此可见，window是没有实体的，window中的实体就是View，通过操作View完成界面管理。
```
        public interface ViewManager
        {
            public void addView(View view, ViewGroup.LayoutParams params);
            public void updateViewLayout(View view, ViewGroup.LayoutParams params);
            public void removeView(View view);
        }
```
  WindowManager内部通过ViewRoot来完成View的绘制，通过WindowManagerService（IPC调用）完成window的添加/跟新/删除。其中删除时记得在View的onDetachedFromWindow回调里做资源回收的工作（终止动画，停止线程）。
  下面我们以Activity为例，看看一个Window创建过程：
1. 如果没有DecorView，那么就创建它  
2. 将View添加到DecorView的mContentParent中
3. 回调Activity的onContentChanged方法通知Activity视图已经发生改变
4. Activity的onResume执行时，DecorView被添加到Window中，得以展示出来。

# 九，动画
Andoird的动画分为三大类：View动画，帧动画，属性动画。其实帧动画也属于View动画的一种。
## 9.1 View动画
View动画的作用对象是View，它支持4种动画效果，分别是平移动画、缩放动画、旋转动画和透明度动画。这四种动画既可以通过XML来定义，也可以通过代码来动态创建，对于View动画来说，建议采用XML来定义动画，这是因为XML格式的动画可读性更好。
![](/img/17012398197021.jpg)
那怎么使用View动画呢？
```
    // res/anim/animation_test.xml
    <? xml version="1.0" encoding="utf-8"? >
    <set xmlns:android="http://schemas.android.com/apk/res/android"
        android:fillAfter="true"
        android:zAdjustment="normal" >

        <translate
          android:duration="100"
          android:fromXDelta="0"
          android:fromYDelta="0"
          android:interpolator="@android:anim/linear_interpolator"
          android:toXDelta="100"
          android:toYDelta="100" />

        <rotate
          android:duration="400"
          android:fromDegrees="0"
          android:toDegrees="90" />

    </set>
```
```
    Button mButton = (Button) findViewById(R.id.button1);
    Animation animation = AnimationUtils.loadAnimation(this, R.anim.animation_
    test);
    mButton.startAnimation(animation);
```

## 9.2 帧动画
类似于View动画，只是改成AnimationDrawable来使用帧动画。注意过大的图片会有OOM的问题。
```
        // res/drawable/frame_animation.xml
        <? xml version="1.0" encoding="utf-8"? >
        <animation-list xmlns:android="http://schemas.android.com/apk/res/android"
            android:oneshot="false">
            <item android:drawable="@drawable/image1" android:duration="500" />
            <item android:drawable="@drawable/image2" android:duration="500" />
            <item android:drawable="@drawable/image3" android:duration="500" />
        </animation-list>
```
```
        Button mButton = (Button)findViewById(R.id.button1);
        mButton.setBackgroundResource(R.drawable.frame_animation);
        AnimationDrawable drawable = (AnimationDrawable) mButton.getBackground();
        drawable.start();
```
## 9.3 属性动画
在一个时间间隔内完成对象从一个属性值到另一个属性值的改变。因此，属性动画几乎是无所不能的，只要对象有这个属性，它都能实现动画效果。
  比较常用的几个动画类是：ValueAnimator、ObjectAnimator和AnimatorSet。属性动画除了通过代码实现以外，还可以通过XML来定义。属性动画需要定义在res/animator/目录下，在XML中可以定义ValueAnimator、Object-Animator以及AnimatorSet，其中<set>标签对应AnimatorSet, <animator>标签对应ValueAnimator，而<objectAnimator>则对应ObjectAnimator。
  官方推荐属性动画，灵活易操作，同时点击事件无需特殊处理。

# 十，关于Android学习的一些感悟
   很久之前看到的一句话，非常触动，Andorid的能力体现在Android之外。Android属于上手非常快，但是深度可以非常深。内部涉及的知识有Linux内核的各种知识（从驱动到内存管理，进程管理等），Android虚拟机，Android架构设计，一些特定能力的c++库（多媒体，opengl，webkit）等等。
   Andorid的源码非常异常庞杂，在深入学习的过程中特别容易深陷调用泥潭。建议阅读的时候抓大放小，不用关注调用链，重点关注模型。看的时候想一想它为什么这么设计，有什么好处。
   另，关于网络上的android资料，推荐优选官网资料。https://developer.android.com/guide?hl=zh-cn
   这里歪个楼，关于ios学习的资料，也同样推荐官网以及他的WWDC视频资料。