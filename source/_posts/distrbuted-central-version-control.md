---
title: 分布式与集中式版本管理
date: 2017-05-22 17:04:00
---

## 为什么分布式版本管理工具会比集中式对于分支的合并更友好一些？
### 以Git与SVN为例：
1. Git的每一个节点拥有着完整的版本信息，任何一个节点都可以重新构建成一个新的完整的Server，而SVN所有的版本信息集中在Server端管理。
2. SVN分支相对Git而言并不是真正意义的分支，实质上是一种虚拟拷贝，SVN在branch的内容没有修改时，会直接引用trunk，不会添加新的拷贝，一旦修改将会在相应的分支位置生成出一份拷贝。
3. SVN的管理单元为文件夹，Git等工具管理单元为工程目录（导致Git想要单独clone一个子文件夹难以做到）

### SVN的branch管理如下：
```
      1   2   4     6     8   9
trunk o-->o-->o---->o---->o-->o      "the merge commit is at r9"
       \
        \   3     5     7
b1       +->o---->o---->o
```
```
           12        14
trunk  …-->o-------->o
                                     "Okay, so when did we merge last time?"
              13        15
b1     …----->o-------->o
```
如图，由于设计（2）（3）SVN的trunk不会被保存到任何的信息，当b1的修改被merge回trunk时。因为对于SVN来说，b1与trunk一样，仅仅是一个不同位置的文件夹，branch的任何一次提交都如同在trunk内部的一个文件夹内提交，只是实际上branch没有在trunk内部，很好的隔离开了两边的代码。这样的分支设计注定了，SVN分支不适合长期的维护和双向merge，因为在长期branch的维护过程中，两边的修改集都不会有任何的互通（如上图的Commit 3，5，7，13，它们在merge时，trunk不会得到任何关于它们的信息），双向merge的问题更加糟糕，会造成更加混乱的结果。

### Git的branch管理如下：
```
                        v master
         a…   b…   c…   f…
bob      o<---o<---o<---o
                   ^
                   |    d…   e…
                   +----o<---o
                             ^ origin/master

         a…   b…   c…   d…   e…
origin   o<---o<---o<---o<---o
                             ^ master

```
```
                                 v origin/master
                                 v master
         a…   b…   c…   f…       1…
bob      o<---o<---o<---o<-------o
                   ^             |
                   |    d…   e…  |
                   +----o<---o<--+

                                 v master
         a…   b…   c…   f…       1…
origin   o<---o<---o<---o<-------o
                   ^             |
                   |    d…   e…  |
                   +----o<---o<--+
```
如上图所示，根据（1）设计，每一个维护的分支在merge之后都有着全部的版本信息，实际上基于此设计Git与Mercurial给出另一种merge方式“rebase”。而且实际上在分布式中并没有SVN上的trunk的概念，master本质也是一个branch。

在SVN 1.5.0之前没有存储任何的branch信息，branch是一份如假包换的纯拷贝，也没有svn merge操作。这时的SVN版本管理是一个纯树状结构，但笔者个人认为设计上应该是线状的，branch仅是辅助工具，而分布式版本管理则是一个有向无环图(DAG)，自然，在设计上DAG的结构更容易管理分支。
因此，笔者个人认为，SVN的设计开始时就是为迭代式的软件工程设计的。

## 总结
集中式管理虽然在分支管理上不如分布式友好，但是也有其他优点，比如，由于设计（1）导致.git与.hg等版本信息管理文件的大小会比.svn大的多，经过长时间迭代就会发现.git会比工程本身都要大很多，而且特别是对于较大的二进制文件，在没有优化过的.git，几乎是在版本信息里存储了一个压缩副本，迭代时间长了之后，.git会大到根本无法下载，必须使用[git-lfs](https://git-lfs.github.com/)或者[git-svn](https://git-scm.com/docs/git-svn)扩展插件来单独维护较大的二进制文件。
所以，对于多人协作服务器开发或者资源较少的App开发，笔者认为分布式管理是一个更好的选择，而对于二进制文件较多的游戏开发，最好采用集中式版本工具管理资源（据说在二进制文件优化这一点上，付费的Perforce将会比SVN优化的更好）。同时，如果有需求需要文件夹管理权限，那么，Git等工具就很难做到，需要使用SVN, Perforce等工具。至于联网等限制条件，笔者认为并不是时下开发的主要限制因素，因此，不再此文讨论。

## 常用工具
集中式管理工具：SVN, CVS, TFS, VSS, ClearCase, Perforce
分布式管理工具：Git, Mercurial, Bazaar

参考资料: 
[how-and-or-why-is-merging-in-git-better-than-in-svn](http://stackoverflow.com/questions/2471606/how-and-or-why-is-merging-in-git-better-than-in-svn)
