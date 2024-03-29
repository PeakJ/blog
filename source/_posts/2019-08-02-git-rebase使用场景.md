---
title: git rebase使用场景🤣
date: 2019-08-02 11:02:16
tags:
- git
categories:
- 积累
---

## 简介
git rebase 俗称变基，作用是**将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样**。这样操作后，提交历史会变得简洁，可读性更高，因为提交记录改变了时间线，把某个完整功能的提交记录整合在了一起，使得这期间其他人的提交记录不会穿插到其中，干净舒爽！🚾👯‍♂️
<!-- more -->
## 原理
它的原理是首先找到两个分支（当前分支 experiment、变基操作的目标基底分支 master）的最近共同祖先 C2，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件，然后将当前分支指向目标基底 C3, 最后以此将之前另存为临时文件的修改依序应用。
![image](https://s2.ax1x.com/2019/08/02/edhOnf.png)

## 重点  ❗❗❗
具体该如何操作网上有很多文章，这里只是记录一下git rebase使用时该注意什么

> 需遵循的金科玉律：不要对在你的仓库外有副本的分支执行变基。

如果你遵循这条金科玉律，就不会出差错。 否则，人民群众会**仇恨**🌚你，你的朋友和家人也会**嘲笑🌚你，唾弃🌚你**。


只要你把变基命令当作是在推送前清理提交使之整洁的工具，并且只在**从未推送至共用仓库的提交上**执行变基命令，就不会有事。 假如在那些已经被推送至共用仓库的提交上执行变基命令，并因此丢弃了一些别人的开发所基于的提交，那你就有大麻烦了，你的同事也会因此**鄙视🌚你**。

## 变基 vs. 合并🌏
你一定会想问，到底哪种方式更好。 在回答这个问题之前，让我们退后一步，想讨论一下提交历史到底意味着什么。

有一种观点认为，仓库的提交历史即是 记录实际发生过什么。 它是针对历史的文档，本身就有价值，不能乱改。 从这个角度看来，改变提交历史是一种亵渎，你使用 谎言 掩盖了实际发生过的事情。 如果由合并产生的提交历史是一团糟怎么办？ 既然事实就是如此，那么这些痕迹就应该被保留下来，让后人能够查阅。

另一种观点则正好相反，他们认为提交历史是 项目过程中发生的事。 没人会出版一本书的第一版草稿，软件维护手册也是需要反复修订才能方便使用。 持这一观点的人会使用 rebase 及 filter-branch 等工具来编写故事，怎么方便后来的读者就怎么写。

现在，让我们回到之前的问题上来，到底合并还是变基好？希望你能明白，这并没有一个简单的答案。 Git 是一个非常强大的工具，它允许你对提交历史做许多事情，但每个团队、每个项目对此的需求并不相同。 既然你已经分别学习了两者的用法，相信你能够根据实际情况作出明智的选择。

> 总的原则是，只对尚未推送或分享给别人的本地修改执行变基操作清理历史，从不对已推送至别处的提交执行变基操作