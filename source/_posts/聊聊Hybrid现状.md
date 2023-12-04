---
title: 聊聊Hybird App
date: 2023-07-20 15:30:23
tags: 大前端
---

引言：本文是一篇关于Hybird技术的综述文
<!--more-->
# 一，从大前端聊到Hybird App
   从 2017 年开始，GMTC“移动技术大会”就更名为“大前端技术大会”。为了顺应这种趋势，很多大公司的组织架构也做了相应的调整，把前端团队和 iOS、Android 一起合并为大前端团队。大前端的好处是**跨平台和动态性**，这两个能力也是相辅相成。
   国内外这几年产生了非常多的跨平台开发方案，基于跨平台技术开发出来的App就叫混合应用Hybird App。跨平台开发方案相同的理念都是基于前端开发技术和语言进行应用开发，然后一套代码运行在多套系统上（Android，iOS），达到跨平台的效果。总结起来就是向上对接了前端生态，向下对接了原生渲染。
   目前提供跨平台能力的Framework非常多，当前热度比较高的是Flutter, React Native, Ionic，Xamarin等等。这些Framework帮我们抚平了平台差异性代码，给我们提供了封装好的组件或模块，提供了热重载能力等等，总之让混合应用的开发变的更容易。
   个人理解混合应用的好处：
* 一个是降低了开发维护成本；
* 另一方面前端技术栈决定了可能具有的动态性。让我们即使不通过发布新版本，也能实现各式各样的运营活动和个性化推荐；
* 最后是接近原生的交互渲染体验；

这几年Hybrid App变得越来越流行，微信、支付宝、咸鱼、抖音、头条、美团等等都是Hybrid App。
   Hybird App和web及原生应用的侧重点不同，如下图。具体要选择哪种方式开发你的引用，取决于场景需求。假如你只是需要一个简单的性能要求不高的公告页面（公告内容随时变更），那应用里加个webview页面是首选；假如你需要大量用到原生功能，或者你对性能有很高要求，或者你需要一些底层库的功能，比如地图app，音视频，蓝牙等功能，那原生应用是首选；假如你的团队技术沉淀偏前端，那多端开发的低成本方案优选混合应用。
   其实目前很多应用都会综合考量，几种方案都会用到，根据场景选择不同方案。比如淘宝很多一级页面是原生的，很多二级页面都是基于Flutter技术搭建的。

   ![](/img/17012574345277.jpg)

# 二，Hybird App的发展历史
## 2.1 热修复/插件化时期
  最早的时候是2016年左右有一批**热修复/插件化框架**，引起一波风潮，例如微信的Tinker、美团的Robust、阿里的AndFix。大概的思路是采用native hook的方式，通过multiDex拆包来完成部分逻辑动态下发加载。
  轰轰烈烈了一阵子之后就没人提了，分析起来原因：
  * 一个是热修复技术因为苹果商店的限制无法应用到iOS上，双端版本维护变的更复杂；
  * 一个是热修复给国内的Android 生态也带来一些不太好的影响，比如增加用户 ROM体积占用、App启动变慢15%、OTA首次卡顿等。特别是 Android Q之后，动态加载的Dex都只使用解释模式执行，会加剧对启动性能的影响。因为性能的问题，目前大公司基本暂停了全量用户的热修复，只使用热修复用于灰度和测试。
## 2.2 跨平台方案的火热发展
  跨平台方案React Native诞生于2013年，是Facebook内部项目。2015年被Facebook开源。一经开源热度很高，本身也一直在不断迭代。有很多产品都是基于React Native开发的，比如Facebook，Instagram，Walmart，以及最终放弃了RN的Airbnb。
  以后跨平台方案层出不穷，国外的Flutter, Ionic, Xamarin, PhoneGap, NativeScript等等。国内的Weex，微信小程序，及各种基于RN, Flutter等的定制化改造方案。
  目前来说，国内各大厂旗下的App使用情况看起来，最热的就是Flutter, Weex，React Native, 微信小程序。这个会在后面一一展开来讲。
  总的来说，现在的跨平台方案分为这三大类：
  * 浏览器渲染+js-native调用功能API：小程序
  * 原生组件渲染+JS/前端框架：RN, Weex
  * 自会渲染+跨端开发语言：Flutter
## 2.3 游戏领域的跨平台和动态性
  单说下游戏领域，游戏引擎的设计初衷决定了它天然就有很好的跨平台性。这体现在两点，一个是语言级别的，一个是图形渲染方面的。这里以Unity为例来聊聊。
  Unity的开发语言是C#，C#被编译器编译成CIL，CIL决定了语言的跨平台型。下层通过Mono虚拟机或者IL2CPP的方式运行在不同平台上。图形渲染方面，游戏引擎本身帮我们适配了不同平台的graphics API，比如Unity支持DirectX(windows), Metal(Apple devices), OpenGL, and Vulkan(android) graphics APIs。
  至于动态性，游戏中常见的胶水语言是lua和js，有成熟开源方案如xlua，slua，大型游戏公司内部都会有自己的方案。

# 三，当前热门的Hybird Framework
## 3.1 微信小程序
  从技术上说，小程序是在一个应用程序中再嵌入另一个应用程序，也就是说微信本身就是一个应用程序，而小程序就是微信应用程序之上的程序，这在国内独有现象，国外是没有的。
  从商业角度讲，这进一步把微信应用，打造成了一个操作系统平台之上的平台，或者 AppStore之上的AppStore。开发者不仅可以使用这些超级App提供的技术能力和触达用户的渠道，还能轻易地实现跨端，节约成本。
  所以微信小程序的跨平台性不是小程序本身提供的，更多是微信这个超级App载体提供的。小程序大部分的展示界面是调用浏览器WebView渲染界面的。
  架构方面最大的特点是采用了双线程的开发模式，隔离了 JS 逻辑和 UI 渲染。小程序的渲染层和逻辑层分别由 2 个线程管理：渲染层的界面使用了 WebView 进行渲染，逻辑层采用 JsCore 线程运行 JS 脚本。一个小程序存在多个界面，所以渲染层存在多个 WebView 线程。这两个线程的通信会经由微信客户端（下文中也会采用 Native 来代指微信客户端）做中转，逻辑层发送网络请求也经由 Native 转发，小程序的通信模型下图所示。这种逻辑层与UI层的隔离架构的好处就是开发者的js代码都要经过微信平台的转发，微信平台的管控力度会更加强。
  渲染层有一套自己的开发框架和语言，包括用于描述页面基础组件的 WXML（WeiXin Markup Language），以及用于描述页面样式的 WXSS （WeiXin Style Sheets）。WXML是一种带有数据绑定的DSL, 数据驱动界面变化。开发者需要进行界面变化时，只需要通过在逻辑层执行 setData 把变化的数据通过 Native 层传递到渲染层，小程序会进行 Dom Diff（DOM 结构对比并进行最小化变更的算法）等流程，最后把正确的结果更新在 Dom 树上。


  ![](/img/17014002807874.jpg)
不过这几年，微信小程序内部也在不断优化，RN-like,Flutter都有去借鉴靠近。
RN-like思想就是说：

![](/img/17015041336430.jpg)
当 WXML/WXSS 发生改变，也就是 UI 页面发生改变的时候，小程序前端公共库（WXA Framework）会进行一些内部运算，以操作指令的形式将变化结果提交给 C++，具体的布局计算、CSS 样式更新和 DOM 结构变化都是由 C++ 这一层实现的。C++ 计算完成后，再决定是调用 WebView 渲染组件，还是调用原生视图渲染组件。
## 3.2 React Native
  React Native的背景前文已经介绍。它的应用层是用JS语言，基于React框架进行开发，运行在Android，iOS, Windows上。
  总的来说，React是通过yoga做跨平台布局适配，然后js跨语言调用原生接口直接操作原生组件完成绘制。
  
![](/img/17014216780920.jpg)

![](/img/17014137433600.jpg)
  React的架构版本有两个，2022年3月的0.68版本做了很多的优化。
### 1）RN旧架构
  旧的架构中是有三个有3个线程：
* JS thread。JS代码执行线程，负责逻辑层面的处理。Metro（打包工具）将React源码打包成一个单一JS文件(图中JSBundle)。然后传给JS引擎执行，现在ios和android统一用的是JSC。
* UI Thread(Main Thread/Native thread)。这个线程主要负责原生渲染（Native UI）和调用原生能力(Native Modules)比如蓝牙等。
* Shadow Thread。 这个线程主要是创建Shadow Tree来模拟React结构树。Shadow Tree可以类似虚拟dom。RN使用Flexbox布局，但是原生是不支持，所以Yoga就是用来将Flexbox布局转换为原生平台的布局方式。

  ![](/img/17014136962275.jpg)


  其中JavaScriptCore是一个JS引擎，IOS系统自带了该引擎。Android没有自带，所以React Native会将JSC和app打包在一起，这会增加一点code size。
  Shadow Thread中有个UI Mageger来做DomTree转化适配工作。React 会执行你的代码并在 JavaScript 中创建一个 ReactElementTree。基于这棵树，渲染器在 C++ 中创建一个 ReactShadowTree。 布局引擎使用此阴影树来计算主屏幕的 UI 元素的位置。一旦 Layout 计算的结果可用，影子树就会被转换为 HostViewTree，它由 Native Elements 组成。 （例如：ReactNative 元素会被分别翻译成 Android 中的 ViewGroup 和 iOS 中的 UIView）。总结起来就是一个**ReactElementTree(JS)->ReactShadowTree(c++)->HostViewTree(Native)**的过程。这里面也涉及大量的JS-C++的跨语言通信，也就是Bridge的过程。
  
  旧版本的RN Bridge是用Java/C++编写的，只能和JSC一起工作。它允许JS线程和Native线程通过一个自定义的通信传输协议来通信。 Bridge两侧是通过异步发送序列化成JSON的消息通信的，大量通信的时候会有乱序阻塞的问题。比如当页面中有大量元素且用户快速滚动时，可能会出现白屏。这是由于Native的滚动事件会经过Bridge传递给JS线程，JS线程将新的布局信息传递给Shadow线程计算，然后将计算后的信息经过Bridge传递给native。
  旧架构的问题主要集中在Bridge上，Bridge有一些固有的限制：
* 它是异步的：某个层将数据提交给桥，再异步地"等待"另一个层来处理它们，即使有时候这并不是真正必要的。
* 它是单线程的：JS 是单线程的，因此发生在 JS 中的计算也必须在单线程上进行。
* 它带来了额外的开销：每当一个层必须使用另一个层时，它就必须序列化一些数据。另一层则必须对其进行反序列化。这里选择的格式是 JSON，因为它的简单性和人的可读性，但尽管是轻量级的，它也是有开销的。

### 2）RN新架构
新架构主要改善Bridge问题，另一种通信机制：JavaScript 接口（JSI,C++编写，全局注入的实现方式）。JSI 是一个接口，允许 JavaScript 对象持有对 C++ 的引用，反之亦然。
  一旦一个对象拥有另一个对象的引用，它就可以直接调用该对象的方法。例如一个 C++ 对象现在可以直接调用一个 JavaScript 对象在 JavaScript 环境中执行一个方法，反之亦然。
  JSI的好处是：同步，并发，开销低，类型安全，同时JSI机制和JSC解耦了，实际上，从 React Native 0.70版本开始Hermes已经是RN默认的JVM了。
  新版本在JSI基础上，引入了：
* 新的原生模块管理体系Turbo Modules，一个支持与本地代码高效、灵活集成的框架。
* Fabric渲染器和组件，它提供了更好的功能、跨平台的一致性和渲染性
* Codegen静态类型检查器，它通过 JavaScript 的静态类型化，生成新架构所需的 C++ 模板。
 ![](/img/17014159590494.jpg)

   
## 3.3 Flutter
  Flutter是Google于2017年开源的跨平台项目，Flutter是Dart语言编写，运行Fuchsia、Android、iOS、Linux、macOS和Windows平台上。Flutter设计之初的原则：
  * 性能至上。内置布局和渲染引擎，使用 Skia 通过 GPU 做光栅化。选择 Dart 语言作为开发语言，在发布正式版本时使用 AOT 编译，不再需要通过解析器解释执行或者 JIT。并且支持 Tree Shaking 无用代码删除，减少发布包的体积。
  * 效率至上。在开发阶段支持代码的Hot Reload，实现秒级编译更新。重视开发工具链，从开发、调试、测试、性能分析都有完善的工具。内置 Runtime 实现真正的跨平台，一套代码可以同时生成 Android/iOS 应用，降低开发成本。

  向上，Flutter的开发里面是一切皆Widget，Flutter官方提供了非常丰富的小组件，供大家自由组合。
  向下，渲染并不是借助原生组件来绘制，而是C++层组合出Render Tree后直接调用Skia渲染引擎接口。这样做的好处是缩短了渲染链路，提升了效率；双端统一使用Skia, 保证了渲染的跨平台性。对于Android，可以直接调用系统自带的skia库接口，但是ios平台，需要在Flutter中额外集成一个Skia库，会带来code size的增大。
  Flutter有一个比较大的缺点就是基本无法动态更新。出于效率考虑Dart编译成AOT 代码，Google Play和App Store都是不允许动态更新更新可执行文件的。
  ![](/img/17014135819723.jpg)

![](/img/17014019062003.jpg)
## 3.4 简单比较下
RN: 
好处：原生渲染，支持Node.JS, NPM包管理工具使用方便，支持Hot Reload开发调试方便。
不足：官方自定义模块不足，复杂UI,特效,交互不是最优选。

Flutter:
好处：性能高，支持Hot Reload，官方组件全，适配Fuchsia，UI流程
不足：学习成本高（Dart），包体积比原生应用大。

小程序：
好处是借助微信等超级App的渠道，非常容易实现跨平台。不足是毕竟是直接在浏览器内核渲染，性能/UI流畅度/白屏问题都要差一些。
不过近几年微信内部也在做借鉴RN-like,flutter的优化。

Weex:
上面的详细介绍中没有提Weex,Weex最近几年风头没有之前那么热了。最初Weex诞生于RN刚出来的那年，架构也比较像RN, 是阿里第一个破10Kstar的项目。2020年Weex推出了2.0，做了很多优化：复用了Flutter Engine大部分代码 改成依赖统一的图形库Skia自绘渲染；JS引擎层面，安卓把JavaScriptCore换成了QuickJS，用bytecode 加速二次打开性能，并且结合 Weex js bundle 的特点做针对性的优化。

# 四，一些背景知识的补充
## 4.1 Unity之Mono vs IL2CPP
Mono vs IL2CPP：
  * Mono: Xamarin主持的开放源代码的工具，可以理解为开源的.net framework。Mono组成组件：C# 编译器，CLI虚拟机，以及核心类别程序库。
  * IL2CPP: IL2CPP编译器+IL2CPP VM。前者用来讲CIL转译为Cpp代码，然后再利用不同平台的优化过的编译器，编译为对于平台的目标代码; 后者是对等MONO的内存托管机制的一种运行时的内存管理器。
  
Mono每个平台一个VM, 维护工作量大，Mono官方维护困难；Mono的版权受限导致的c#运行时版本受限，很多高级c#特性无法使用。同时Mono执行效率低，IL2CPP是他的1.5-2倍。所以从unity4.x版本开始unity就提供了IL2CPP的支持，并且官方更推荐IL2CPP方式。

## 4.2 移动端浏览器内核
  目前，移动设备浏览器上常用的内核有Webkit，Blink，Trident，Gecko等，其中iPhone和iPad等苹果iOS平台主要是**WebKit**，Android 4.4之前的android系统浏览器内核是WebKit，Android4.4系统浏览器切换到了Chromium(内核是Webkit的分支**Blink**)，Windows Phone 8系统浏览器内核是Trident。
### 1）WebKit项目：
WebKit项目是苹果公司在2005年发起的一个新的开源项目，是Safari浏览器的内核，是目前的主流浏览器渲染引擎。
WebKit架构如下： 
* WebCore部分：包含了目前被各个浏览器所使用的WebKit共享部分，是加载和渲染网页的基础部分，具体包括HTML解释器、CSS解释器等。   
* **JavaScriptCore**引擎：是WebKit中的默认JavaScript引擎。在Google的Chromium项目中，它被替换为V8引擎。 
* WebKit Ports部分：是WebKit红的非共享部分，属于WebKit被移植的模块。由于不同浏览器使得平台差异、依赖的第三方库和需求不同，从而导致多种WebKit版本。
* WebKit嵌入式接口：指在WebCore和javascript引擎之上的一层绑定和嵌入式编程接口，可以被各种浏览器调用。 
![](/img/17013389063564.jpg)
### 2）Chromuim项目：
Chromuim项目是Google公司以苹果开源项目WebKit为内核，创建的一个新的项目，该项目的目标是创建一个快速的、支持众多操作系统的浏览器。在Chromium项目基础上，Google发布了自己的浏览器产品Chrome(注意：Chromuim本身就是一个浏览器)。
   从Webkit复制出来并独立运作Blink内核项目是Chromuim项目的一部分，Blink后续作为Android4.4及以上系统浏览器（4.4之前是webkit内核），Chrome, Opera, Microsoft Edge等诸多基于Chromium的浏览器采用的内核。
   Blink内核中使用的js引擎是**V8**。
![](/img/17013437488886.jpg)

### 3）国内项目：
之所以单独讲，是因为国内android手机自带浏览器并不是chrome，而是生产厂商自己的浏览器，不过他们大多是基于chromium做改造。这里要单独提下腾讯x5内核，是由腾讯基于开源Webkit深度优化而来，最新的版本使用的是Blink内核。QQ浏览器和微信（包括小程序）使用的就是x5内核。

# 五，参考和扩展阅读
https://zhuanlan.zhihu.com/p/137780634
https://www.zhihu.com/question/26595681
https://cloud.tencent.com/developer/article/2084982
https://www.mobileappdaily.com/knowledge-hub/best-hybrid-app-frameworks
https://cloud.tencent.com/developer/article/2181095
https://juejin.cn/post/7159484908786843662
https://zhuanlan.zhihu.com/p/373582962
https://zhuanlan.zhihu.com/p/70070316
https://www.infoq.cn/article/omef0qu4qllcy3drvep9