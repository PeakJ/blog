---
title: 'localStorage读取性能[转]'
date: 2017-11-16 17:26:10
tags: 
- localStorage
categories: 
- 优化
---
[原文地址](https://www.epubit.com/book/detail/7751)

作者：Nicholas Zakas

在Web开发人员使用的工具中，Web Storage（[http://dev.w3.org/html5/webstorage/](http://dev.w3.org/html5/webstorage/)）迅速成为了HTML5新增的最受欢迎的功能。具体来说，localStorage提供了快速简单的客户端数据存储功能，可以跨多个会话存储数据，这使得localStorage在世界各地Web开发人员中被广为应用。通过使用简单的键值接口，我们看到很多网站以各种独特而有趣的方式应用localStorage这一技术。

Disqus（[http://www.disqus.com/](http://www.disqus.com/)），是一个非常流行的反馈管理系统。它使用localStorage来实时保存您正在输入的评论。因此，即使有可怕的意外发生，你也可以重启浏览器，从出现问题之前的地方继续。

谷歌（[http://www.google.com/](http://www.google.com/)）和Bing（[http://www.bing.com/](http://www.bing.com/)）用localStorage存储JavaScript和CSS，来改善他们的移动网站性能。
<!-- more -->
在我了解的案例中，Google/Bing的这种方式似乎越来越受人们的欢迎。部分原因是由于HTML5 application cache使用困难，部分原因是该技术得到了Steve Souders和其他人的青睐。事实上，随着我越来越多地和人们提及localStorage以及用它来存储UI相关信息是多么的有用，我发现更多人开始尝试这种技术。

我发现一个令人费解的现象，人们在使用localstorage时有一个内定不成文的假设：从localStorage读取数据开销不大。我曾经从其他开发者那里听到传言说遇到异常的性能问题，所以我设计了一个实验来量化localStorage的性能特点，以确定用它读取数据的实际成本。

2.1　基准测试

不久前，我创建并共享了一个简单的基准测试，来测量从localStorage取值和从一个对象属性取值的性能。有人基于这个测试做了改良，并开发了更可靠的版本（[http://jsperf.com/localstorage-vs-objects/10](http://jsperf.com/localstorage-vs-objects/10)）。最终结果是：每个浏览器中，从localStorage读取数据比从对象属性中读取相同数据慢几个数量级。到底有多慢呢？请看图2-1所示（数字越高越好）。
![图2-1基准测试](https://upload-images.jianshu.io/upload_images/1231991-699ca44578825027.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


看了这个图后，你可能会感到困惑，因为它似乎没有展示出从localStorage中读取数据的结果。事实上它被展现出来了，你看不到它的原因是这一数字实在太小，在图示比例上甚至不可见。除了Safari 5可以显示实际的localStorage的读取数据结果，所有其他浏览器的巨大差异使得你根本不可能在这张图上看到实际数据。当我调整y轴的值后，你才可以看到不同浏览器中的测量结果。

通过改变y轴的比例，现在你可以看到从local Storage和从对象属性读取数据的真实对比（见图2-2）。可以看出，结果仍然如此，几乎到了滑稽的程度，这是为什么呢？
![图2-2测试结果](https://upload-images.jianshu.io/upload_images/1231991-f498c766aa81d4b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


2.2　这是怎么回事

为了保持跨浏览器会话，localStorage的数据被写入磁盘中。这意味着当你从localStorage中读取数据时，你实际上是从硬盘驱动器上读取这些字节。读写硬盘开销高昂，特别是相对于读写内存来说。从本质上讲，这正是我的基准测试所测试的内容，即从缓存（对象属性）读取数据和从磁盘（localStorage）读取数据的速度比较。

更有趣的是，localStorage按源存储数据，这意味着，在同一时间内，浏览器中的多个标签页可以访问相同的localStorage数据。

这对于那些需要弄明白如何实现各标签页同步访问localStorage的浏览器开发商来说十分头疼。当需要从localStorage中读取数据时，浏览器首先需要停下来看看是否有其他标签页正在访问同一数据区域。如果是的话，浏览器必须等到之前的访问操作完成，才可以读取该值。

因此，与从localStorage读取数据相关的延迟是不定的，它在很大程度上取决于在那个时间点上浏览器还在进行的其他操作。

2.3　优化策略

既然从localStorage读取数据有一定开销，那么这会怎样影响你如何使用这一技术呢？在下结论之前，我运行了另一个基准测试（[http://jsperf .com/localstorage-string- size](http://jsperf.com/localstorage-string-size)），以确定从localStorage读取不同大小数据的效果。我们保存4种不同大小的字符串（100个字符、500个汉字、1000个字符和2000个字符）到localStorage中，然后把它们读取出来。结果有点令人惊讶，在所有浏览器中，读取数据量的大小不会影响读取速度的快慢。

我测试了很多次，并恳请我的Twitter粉丝提供更多的信息（[https://twitter.com/ slicknet/status/139475625793699840](https://twitter.com/slicknet/status/139475625793699840)）。可以肯定的是，对于不同的浏览器会有些许差异，但这些差异都不大，并没有实质性的不同。我的结论是：从单一localStorage键值中读取的数据量对速度没有影响。

我又进行了另一个基准测试（[http://jsperf.com/localstorage-string-size-retrieval](http://jsperf.com/localstorage-string-size-retrieval)）来检验我的新结论，最好尽可能地减少读取数据的次数。和此前的基准测试结果类似，在大多数浏览器中，读取100个字符10次比一次读取10 000字符慢90%左右。

鉴于此，从localStorage读取数据的最佳策略是使用尽可能少的键值，存储尽可能多的数据。因为读取10个字符和读取2000个字符所需时间大致是相同的，所以你应该尝试把尽可能多的数据保存为一个键值对应的值。每次调用getItem()（或从localStorage读取属性）都会增加时耗，所以一定要确保每次访问读取数据最大化。对于任何一个变量或对象属性，你越快将它读取到内存，后续的所有操作也会越快。

2.4　跟进

这篇文章一经发表就引发了大量围绕localStorage性能的讨论。讨论最开始出现在Mozilla 的Chris Heilmann的博客文章中，题为“localStorage没有简单的解决方案”。在文章中，Chris认为localStorage作为一个整体存在性能问题。在很多博文讨论（其中也包括我的）之后，我最终能够联系上Firefox负责实现localStorage的工程师Jonas Sicking。事实上，localStorage所存在的性能问题并不只是比从对象读取数据花费时间长一些那么简单。问题的关键是，localStorage是一个同步API，这本身就使浏览器实现这个API时捉襟见肘。所有的localStorage数据都存储在磁盘文件上。这意味着，为了让您可以用JavaScript访问这些数据，浏览器必须先将该文件读取到内存中。当读取操作发生时性能问题便产生了。当第一次访问localStorage就可能产生性能问题，但在随后的读取操作中浏览器有可能会冻结。在处理少量数据时，这可能不是一个大问题，但如果你已经达到5 MB限制时，就会有明显的影响。Firefox采纳的另一种解决方案，是在加载页面的同时读取localStorage数据。这将确保之后对localStorage的访问最快，并有可预测性。这种方法的缺点是对页面加载时间造成不利影响。到我写这篇文章之时，这一问题仍然没有被解决。有人呼吁用一个全新的API来代替localStorage，其他的人则试图修复现有API的问题。不管发生什么情况，不久的将来仍会有客户端数据存储领域的更多研究。不管发生什么情况，最近一段时间内，我们仍然需要在客户端数据存储方面做更多的研究。
