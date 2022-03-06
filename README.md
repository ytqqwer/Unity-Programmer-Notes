
- [Unity Programmer Notes](#unity-programmer-notes)
- [游戏开发技术](#游戏开发技术)
  - [Unity](#unity)
  - [图形学与Unity着色器](#图形学与unity着色器)
  - [热更新](#热更新)
  - [游戏开发](#游戏开发)
  - [网络游戏编程](#网络游戏编程)
- [Programming language](#programming-language)
  - [C#](#c)
  - [Lua](#lua)
  - [C++](#c-1)
- [Programming Design Patterns](#programming-design-patterns)
  - [资料](#资料)
  - [设计模式与游戏完美开发](#设计模式与游戏完美开发)
  - [10个编码原则](#10个编码原则)
- [Community](#community)

# Unity Programmer Notes

学习Unity过程中产生的文档，干货满满，上传到网上做记录。抽空把更多收藏夹中的内容整理进来。

# 游戏开发技术

## [Unity](Unity.md)

[Unity User Manual](https://docs.unity3d.com/cn/2022.1/Manual/UnityManual.html)用户手册已经非常详细了，几乎涵盖了需要知道的所有基础内容。

[Unity 脚本参考](https://docs.unity3d.com/cn/2022.1/ScriptReference/index.html)要放在收藏夹中最显眼的位置。毕竟写代码的时候，IDE只会给出很简略的API说明，还是要不停翻手册。

[欢迎访问 Unity Learn](https://learn.unity.com/)这是个很好的学习资源。翻了很多篇国内的文章，实际上很多都是官方文档的节选翻译或总结。官方的高级课程涵盖了超多内容，很有营养，像是这篇[Optimizing Unity UI](https://learn.unity.com/tutorial/optimizing-unity-ui)。争取成为一个六边形战士。

具体到这个文件中的内容，参照面试题，整理了成吨的Unity知识点，同时做好了相关内容的分类。涉及UI，内存，资源管理，性能调优等主题，具有一定的深度。这些主题涵盖的内容实在太多了，很难全部掌握，具体还是看文件吧。

## [图形学与Unity着色器](图形学与Unity着色器.md)
从几道简单的面试题，扩展成了这么一篇近千行的文档，全是干货。大部分内容来自《Unity Shader入门精要》，许多来自《3D数学基础 图形和游戏开发》的内容作为补充，还引用了一些网站上的内容。

包含3D数学基础知识，渲染管线，光照模型，纹理，透明效果，PBS，渲染优化等内容。

了解OpenGL，[欢迎来到OpenGL的世界](https://learnopengl-cn.readthedocs.io/zh/latest/)

学习ShaderLab，可以参照：[Shader Tutorials by Ronja](https://www.ronja-tutorials.com/)

不过如果让我选，我更喜欢[Shader Graph](https://docs.unity3d.com/Packages/com.unity.shadergraph@13.1/manual/index.html)，外网教程基本用这个做介绍。

关于VFX，这些图形特效的实现方案也是个很大的主题，初学者玩不来，可以看看[Gabriel Aguiar Prod.](https://www.youtube.com/c/GabrielAguiarProd/videos)的油管频道感受一下。

## [热更新](热更新.md)
这是个很庞大的主题。热更新包括资源更新和代码更新，需要一个完善的资源管理方案支持。

主要粘贴了一些介绍[xLua](https://github.com/Tencent/xLua)的文章。比起单纯的使用，理解xLua的底层实现机制也很有益处。 

## [游戏开发](游戏开发.md)
记录一些通用的游戏开发技术。

如何在有限的硬件条件下尽可能呈现多的内容？空间划分算法和可见性算法必不可少，典型的空间换时间，不仅能加快游戏逻辑的执行速度，还可以大幅减低渲染压力。

还有实现游戏AI常见的技术；导航网格和寻路节点等。

## [网络游戏编程](网络游戏编程.md)
身为一个前端，网络知识也必不可少，计算机网络的知识网上有很多。这篇笔记涉及游戏相关的网络技术。

- 网络游戏的物理架构（C/S，P2P）、逻辑架构（MO，MMO）、同步和异步方式
- TCP和UDP，在应用层实现可靠的UDP（KCP）
- 帧同步和状态同步
- 套接字通信

# Programming language

## [C#](Programming%20language/Csharp.md)
微软官方文档[Docs .NET C# 指南](https://docs.microsoft.com/zh-cn/dotnet/csharp/)内容很详细，但只有语言部分。C#和.Net密不可分，对.Net也要有所了解：[.NET API 浏览器](https://docs.microsoft.com/zh-cn/dotnet/api/?view=net-6.0)。

贴一些不错的官方文档：
- [自动内存管理](https://docs.microsoft.com/zh-cn/dotnet/standard/automatic-memory-management)
- [委托简介](https://docs.microsoft.com/zh-cn/dotnet/csharp/delegates-overview/)
- [字符串（C# 编程指南）](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/strings/)
- [装箱和取消装箱（C# 编程指南）](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/types/boxing-and-unboxing)
- [反射 (C#)](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/reflection)
- [特性 (C#)](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/attributes/)

## [Lua](Programming%20language/Lua.md)
好好看一遍《Lua程序设计(第4版)》，这篇笔记中的内容都可以在书中找到。

[Lua 5.3 参考手册](https://www.runoob.com/manual/lua53doc/contents.html)在线文档也挺不错，但是内容编排顺序合理程度不如书本。

[The official definition of the Lua language is its reference manual](https://www.lua.org/docs.html), which describes the syntax and the semantics of Lua, the standard libraries, and the C API.

## [C++](Programming%20language/C++.md)

[Standard C++ Library reference](http://www.cplusplus.com/reference/)
C++标准库有什么一目了然。

如果C#题目有100道，那么C++的题目就有2000道.....不过C++的学习资料也有很多，题目总结也很多，这里就不赘述了，偷个懒。

C++的“传世经典”有很多，重要的是温故知新。亲身感受，只通过刷题构建的知识体系是松散的零碎的，最好从基础一点点学起来。推荐阅读《C++ Primer》（能看懂这本水平已经很强了），《Effective C++》，《More Effective C++》，《Modern Effective C++》（3本Effective必看），《C++ 代码整洁之道》，《深入应用C++11：代码优化与工程级应用》......

# Programming Design Patterns

## 资料

[Game Programming Patterns](https://gameprogrammingpatterns.com/) is a collection of patterns I found in games that make code cleaner, easier to understand, and faster.

[Refactoring.Guru](https://refactoring.guru/) makes it easy for you to discover everything you need to know about refactoring, design patterns, SOLID principles, and other smart programming topics.

[Clean Code: A Handbook of Agile Software Craftsmanship](https://www.goodreads.com/book/show/3735293-clean-code)


## [设计模式与游戏完美开发](Programming%20Design%20Patterns/设计模式与游戏完美开发.md)

需要配合同名书籍《设计模式与游戏完美开发》和[PBaseDefense_Unity3D_Ver5](https://github.com/sttsai/PBaseDefense_Unity3D_Ver5)食用。

这个小演示DEMO使用了多种设计模式。不依靠MonoBehavior不可预测的代码执行顺序，自己接管了所有系统的更新和管理任务，个人比较喜欢这种工作方式，可以专心把精力放在IDE中。

## [10个编码原则](Programming%20Design%20Patterns/10个编码原则.md)
- 代码应该读起来像英语（不需要注解）。
- 你应该花时间给事物好好命名。
- 你的类应该只做一件事（函数也一样）。
- 你应该使用尽可能少的状态。
- 第三次重复代码前没有重构代码的必要。
- 你不应该使用静态和全局状态。
- 高内聚。
- 低耦合。
- 优先使用组合而非继承。
- 遵守迪米特法则（不要和陌生人说话）。

# [Community](Community.md)

收藏的一些好资源，好站点，杂项。

最主要的是为这个[killop/anything_about_game](https://github.com/killop/anything_about_game)找个显眼的地方。

