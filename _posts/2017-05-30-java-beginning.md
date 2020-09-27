---
title: Java入门姿势
tags:
- Java入门
---

> 技术只是工具，文档只是说明书，仅此而已。

# 写在前面
工作4年有余，盲人摸象般的走过弯路，也投机取巧的领悟到过一些类似“编程本质”的东西。现在开始我计划回顾下我的编程生涯。在这里分享，同时也是对之前所学的巩固和整理。
# 理解编程
我先打个比方。人的大脑就像一台电脑主机。比如今天你上班被领导骂了，下班回到家后你将这段记忆从“硬盘”中取出，然后再放到“内存”中翻来覆去的“计算”，最后得出结论“领导是个混蛋”，然后再存储到“硬盘”上。这样，“领导是个混蛋”这段记忆就永久的存储在你的“硬盘”中。
我再举一个例子。你买回一个路由器，一边看说明书一边鼓捣你5分钟就搞定了路由配置。那么，你把说明书全部背下来了吗？显然你不会。
对待技术我们也是如此。
不要试图去记住每个技术的实现细节，因为那样的话你穷尽一生也无法记住如此庞大的数据量。你的“硬盘”是有限的，你的“内存”也是有限的。
你只需做到：**这个问题我知道去哪里找到答案** 就足够了。
不要把太多的东西装到你的脑子里，那样是很愚蠢的。keep you sane。除了工作，你还有生活，不是吗？
# 敲开Java大门的姿势
打开下面的网址，看一下目录，你就可以回来了。不要把过多的经历放到细节上，当你真正需要用到的时候，知道去哪里找就可以了。
[java菜鸟教程](http://www.runoob.com/java/java-tutorial.html)
这里面包含了如何搭建环境，java的基础语法，以及一些基础的类。你甚至可以在网站模拟的环境下进行尝试编程。
现在你可以试着按照“说明书”去尝试编写Java程序了。
# bin目录
    C:\Program Files\Java\jdk1.8.0_121\bin
bin目录下*.exe文件是windows下的执行文件。是java环境下的全部执行文件，这里的javac.exe,javaw.exe,jar.exe,javap.exe都有各自的作用。你可以搜索各自的作用，也可以忽略。你只要记住这个目录是执行目录就可以了。也就是说你在配置环境变量时，告诉windows，我在这里安装了java。
你可能会在各种新手教程中看到执行`java -version`命令来确认是否成功安装java。那我们就来执行一次。
调出cmd
键入`java -version`

> java version "1.8.0_121"
> Java(TM) SE Runtime Environment (build 1.8.0_121-b13)
> Java HotSpot(TM) 64-Bit Server VM (build 25.121-b13, mixed mode)

你可以试着键入`java.exe -version`会输出同样的信息。也就是说你执行了bin目录下的*.exe文件，仅此而已。
环境变量就是告诉windows，这个目录下的所有exe文件，可以在命令控制台直接调用。
# 文档
理论上，学习一种新的语言，应该去它最原始、最官方的地方去找“说明书”。java是sun公司创造后来被oracle公司收购，而java相关api最权威的地址是[java api](http://docs.oracle.com/javase/8/docs/api/)。
同样的，你不需要记住里面的全部内容，你只需要记住当你想详细理解某个类的时候，去这个网址找它详细的说明就可以了。
# jar包
jar是java打包类的合集，也可以理解为别人写好的某些功能的实现。就是说，你需要的大多数功能已经有人写好了，找到这样的jar，你就可以直接调用而不用自己重新去写。
比如，在[cornershop](https://github.com/caiyongji/cornershop)项目中[https://github.com/caiyongji/cornershop/tree/master/WebRoot/WEB-INF/lib](https://github.com/caiyongji/cornershop/tree/master/WebRoot/WEB-INF/lib)目录下的jar就是前辈写好的某些功能的实现，在代码中调用即可。
# 疑难解答
遇到问题时，无论是google还是baidu，你要自己去找解决办法。Java是一门非常成熟且社区完备的语言。你遇到的问题一定有前辈遇到过。
推荐几个社区：
[stackoverflow](https://stackoverflow.com/)
[博客园](https://www.cnblogs.com/)
[csdn](http://www.csdn.net/)
# 就这么简单？
想要入门的话就这么简单。想要掌握的话，写几个小程序即可。想要熟练的话，把文档和教程通读(这里的通读，是全部读完，包括任何细节)。想要精通的话，要再理解java**所有的、全部的**内容基础上，有自己的认识。在这个过程中，如果你试图跳过任何细节，你一定会走弯路。

我不是喂饭的人，知识就在那里。
