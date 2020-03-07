# 简介

## 这本书是如何组织的

在第一章中，我们从了解单仓库开发开始。我们会概括Nx的基本知识，并且了解怎样开始使用Nx。

在第二章中，我们将深入了解库：如何组织它们、命名它们、结合它们，以及其他一些帮助代码复用以及模组化的技巧。

在第三章中，我们将了解在单仓库中如何使用Nx自带工具保证代码质量与一致性。

在第四章中，我们将了解Nx如何使构建更加智能，以及如何在本地和持续集成管道中使用Nx工具。

在第五章中，我们将了解当有着不同发布时间表的多个团队同时在单仓库开发的时会遇到一些常见问题。

在附录甲，我们将了解如何使用其他工具（Angular Console）与Nx交互。

在附录乙，我们将了解所有你可以在Nx中使用的命令，并且这些命令都指向书中的特定章节，从而方便找到更多相关的信息。

在附录丙，我们将了解一些常见的解决问题的方法，并且展示一些常见问题的决策树。

## 格式

*示例代码采用这样的格式*

```javascript
function() {
  console.log('hello world');
}
```

> 注解采用这样的格式，描述那些可以丰富当前论题的信息

![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1") 信息符号突出标示了应当注意的相关信息

![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1") 注意符号标示了常见的陷阱

![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1") 警告符号标示了可能会产生重大影响的关键信息

## 示例参考应用

作为贯穿本书的参考，让我们设想一个虚构的Nrwl航空公司。公司内有三个团队：

* 订票：团队的目标是允许用户预定航班，到达目的地。
* 值机：团队的目标是允许用户对已经下单的航班在线值机。
* 座位图：团队的目标是允许用户在图形化界面选定座位。

一共有四个应用分别独立部署：值机（桌面端与移动端）与订票（桌面端与移动端）。最终用户基于访问的网址（booking.nrwl-airlines.com或是check-in.nrwl-airlines.com）以及随请求发送的浏览器元数据（用来提供桌面端或是移动端体验）访问其中的一个应用。

代码的依赖图是这个样子的：

![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1")

*图-1 示例代码仓库的结构与依赖关系*
