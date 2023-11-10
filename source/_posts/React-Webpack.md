---
title: Webpack&React
date: 2022-10-02 10:30:05
tags: 前端
---
<meta name="referrer" content="no-referrer"/>
引言：本文简介[react框架]及webpack的简介；
<!--more-->

一，[Webpack]简介
------------------------------------------------------------------------------

### 1，Webpack是干什么的

Webpack is a module bundler. Its main purpose is to bundle JavaScript files for usage in a browser, yet it is also capable of transforming, bundling, or packaging just about any resource or asset.

简单说webpack的作用是对项目的js，css, 图片及第三方库等各种资源的打包/解包，依赖链管理等综合构建工具。是目前前端使用最广泛的项目构建工具之一。

![alt](https://img-blog.csdnimg.cn/a0341248625047248693dc6be6da97f0.png)

### 2，Webpack的使用简介

这里只简单介绍下webpack的工程结构。官方的[Getting Started | webpack](https://webpack.js.org/guides/getting-started "Getting Started | webpack")是一个很好的展示。

![](https://img-blog.csdnimg.cn/9a6610daa6f14579be1b27c8a47a83ed.png)

![](https://img-blog.csdnimg.cn/f1e636a7e88e41b6b9502c96d0cd714f.png)

![](https://img-blog.csdnimg.cn/e387001124074258a672dc36685fbb9a.png)

#### 1）工程结构简介

*   src中是工程源码，包括js脚本，css和图片等（放到src/asset下）
*   dist是npm run build构建后的资源（一个或者少量若干个合并优化后的资源文件）
*   package.json中注明需要依赖的第三方库
*   webpack.config.js中注明webpack的build导出目录，执行入口文件，引入的loader及规则，plugin等。不同环境对应不同的config文件。

#### 2）实际工程中会涉及的常见配置

通过demo我们对webpack的工程结构和配置运行有所了解，实际项目中我们还经常会涉及：

*   不同环境（dev,product）下webpack的不同配置（webpack.\[dev/product/common\].js）；
*   不同资源类型的loader引入，如相dom注入css的，less的，图片解析的等等(less-loader, style-loader，html-loader, file-loader等）

![](https://img-blog.csdnimg.cn/04e2cf646ece455faa0fc94a91549177.png)

*   plugins的使用（如应对频繁更新的htmlwebpackplugin，cleanwebpackplugin, 将css独立出来，并优化压缩的minicssextractplugin等）

![](https://img-blog.csdnimg.cn/665a5bd5e14a4de8a6edc3d76ca57f5c.png)

*   不同环境下不同执行入口entry的配置；

![](https://img-blog.csdnimg.cn/72dd62dc953b425787f90f0f70f0e9bb.png)

二，React
-------

### 1，React框架简介

React框架被设计的目的是针对大型复杂的前端工程项目，通过模块化的方式让我们编写扩展性，可维护性更好的代码。React的项目实际上是搭建了一个挂载在根节点上的组件树，而不是DOM树，业务逻辑直接操作的是这个组件树。

React渲染过程本质上是在：根据数据模型（应用状态）来计算出视图内容。

React的核心是“万物皆组件”，其实现特点是：

1.  配合使用jsx，达到动态构建组件的目的。
2.  函数式组件+React Hooks的支持，实现灵活构建组件的目的。

这里展开几个概念：

#### 1）什么是JSX？

JSX是一个 JavaScript 的语法扩展，概括起来就是在js中使用类似[XML](https://en.wikipedia.org/wiki/XML "XML")的语法创建[DOM](https://en.wikipedia.org/wiki/Document_Object_Model "DOM")树。JSX所在的文件的后缀必须是.jsx。

**React中使用jsx的好处是什么呢**？JSX允许在DOM中引入变量，函数等动态值/逻辑，从而可以达到React运行中动态构建组件的目的。

    常量应用程序 = () => {   返回 （     <div>       <p>标题</p>       <p>内容</p>       <p>页脚</p>     </div>   ）；}

#### 2）为什么React官方鼓励函数式组件，而不是类组件？

这里就涉及函数式编程和面向对象编程的区别了（待填充）。目前较新的React库都是基于函数式组件的。

#### 3）现在React最常用的搭配是React + TypeScript，相比js，ts有什么优势？

TypeScript是[JavaScript]的严格语法超集，提供了可选的静态类型检查。TypeScript是为开发大型应用程序而设计的，且可[转译](https://zh.wikipedia.org/wiki/%E6%BA%90%E5%88%B0%E6%BA%90%E7%BC%96%E8%AF%91%E5%99%A8 "转译")成JavaScript，任何现有的JavaScript程序都是合法的TypeScript程序。ts的最重要的好处是静态类型检查，会让很多类型问题更早暴露；兼容已有的大量js库。

### 2，创建React工程

1）创建工程的两种方式

*   React官方提供的CRA
*   通过Vite插件创建

Vite是一种新型前端构建工具，会让前端开发更快捷方便，更推荐。

2）编辑器及常用插件

官方推荐VS Code，常用的插件是格式化prettier，便捷创建组件的ES7+ React/Redux/React-Native snippets。

### 3，工程结构

![](https://img-blog.csdnimg.cn/41a54136d8554e51b3740e1239afd0d5.png)

*   node_modules是继承的第三方库的代码
*   public是网站的公共资源，如图片等
*   src是真正的源码项目，assets中是图片，css等资源。
*   tsconfig是ts转变成js的配置项，不需要关注。
*   vite.config是vite配置文件，也不需要关注。

其他关于webpack的配置项，这里不再详述。

### 4，React是怎么工作的

首先引入一个概念，React直接创建的不是一个真正的DOM树，而是一个ReactDOM树，每个ReactNode是一个组件。

React会监听每个ReactNode的数据状态（state），如果状态改变，则内部会和前一个ReactDOM比较，并计算渲染。拿我们的helloworld工程为例，是创建了一个APP的组件，并最终挂载在root根节点下。

准确来说，以上监听计算刷新ReactDOM的过程，是react-dom这个库是实现的（config中的依赖库）。

![](https://img-blog.csdnimg.cn/e2b30f8d688e4b9f93908809f6d0800f.png)

![](https://img-blog.csdnimg.cn/be98f2b1c73a452ebd11781610d51931.png)

![](https://img-blog.csdnimg.cn/4681c15a00f5491195863d08a7d127d6.png)

### 5，React组件

react组件一般放置在src/component下面，一般来说组件名称和文件名称保持一致。

![](https://img-blog.csdnimg.cn/01aa994e153a4cee9321830aeca97b51.png)

创建组件的几点事项：

#### 1）返回的jsx是多个节点的话，最外层加<Fragment></Fragment>（或者<></>）。

`Fragment`对生成的 DOM 没有影响，也就是说与没有这一层的DOM相同。

#### 2）组件是可以多层嵌套的

#### 3）组件的导出分为默认导出和命名导出。

两者的区别是

*   一个文件只能有一个_默认_导出，但可以有任意多个_命名导出；_
*   _默认导出导入时可以任意命名，命名导出导出导入名称必须匹配。_

![](https://img-blog.csdnimg.cn/5c13b4e1ad0049aa80b1bcb6bfacd2fb.png)

#### 4）尽可能让组件中的变量，常量抽离出来，提高复用性。

#### 5）JSX中可以引入的只能是html节点，react组件，{xxx}。

其中{}中可以是任何东西（字符串，变量，函数等等）。

### 6，React Hooks

React hook是配合函数式组件的机制，提高组件复用性。最基础的hook机制有state和props。

![](https://img-blog.csdnimg.cn/fb0c67f591f0466cae6ea44c372e6d70.png)

#### 1）state和props的区别如上图

state的目的主要是将组件数据的变化和页面刷新绑定。react-dom通过监听组件state的变化触发页面比较刷新逻辑。

props的目的是在组件间传递数据。这个数据除了基本类型，还可以传递函数，以及任意一个react element​(通过props.children来传递，例如字符串、函数、​一段html，react element​等等);

#### 2）其他hook

除了state和props，常用的还有：

*   Context Hooks: 用于多个组件间共享state，避免props地狱。
*   Effect Hooks：允许组件连接到外部系统（非react代码），如拉取网络数据，动画，其他ui库等。

还有Refence Hooks，Performance Hooks等，甚至可以自定义hook。

### 7，React生态环境

React只是一个提供动态交互式渲染界面的库，但是前端项目往往还需要网络，动画，ui，表单，国际化，测试，路由，css等等。所以我们需要一系列库才能完成这些，也就是React生态环境。

目前React生态环境非常丰富，常见的React相关的库及调试环境推荐参考这里：[GitHub - enaqx/awesome-react: A collection of awesome things regarding React ecosystem](https://github.com/enaqx/awesome-react "GitHub - enaqx/awesome-react: A collection of awesome things regarding React ecosystem")

三，总结
----

通过本文，我们了解了：

1, Webpack是做什么的，Webpack工程结构，Webpack的简单配置

2, React框架的中心思想模块化(函数式组件+jsx+hook)及基本用法(with typescripts)。## 目标


