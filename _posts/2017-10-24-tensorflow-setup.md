---
title: （通用）深度学习环境搭建：tensorflow安装教程及常见错误解决
tags: tensorflow 机器学习
---
区别于其他入门教程的“手把手式”，本文更强调“因”而非“果”。我之所以加上“通用”字样，是因为在你了解了这个开发环境之后，那些很low的错误你就不会犯了。
大家都知道深度学习涉及到大量的模型、算法，看着那些乱糟糟的公式符号，心中一定是“WTF”。我想说的是，这些你都不要管，所谓车到山前必有路。
# 所需安装包 #
通常以我的习惯是以最简单的方式来接触一门新的技术，并且尽量抛弃新的（边缘）技术的介入，如果因为一些其他因素来导致学习树的不断扩大，会变得很低效，所以我们直击核心。以最常用的windows环境为例。
这里以**windows7+TensorFlow-gpu1.5+cuda8+cudnn6+anaconda5+python3.6**为例。这里强烈推荐GPU版本，因为深度学习动辄几小时、几天、几周的运行市场，GPU加速会节省你很多时间（甚至电费）。
1. cuda_8.0.61_windows.exe [http://developer2.download.nvidia.com/compute/cuda/8.0/secure/Prod2/local_installers/cuda_8.0.61_windows.exe](http://developer2.download.nvidia.com/compute/cuda/8.0/secure/Prod2/local_installers/cuda_8.0.61_windows.exe)：  从NIVDIA官网下载需要找到历史版本**Legacy Releases**。  tensorflow代码引用的cuda库必须**绝对匹配**，比如tensorflow1.3-1.5都使用cuda8的库，目前（2017-10-24 20:40:53）不支持cuda9库。
这里有一个关于cuda8的补丁，修复了8.0的一些bug[http://developer2.download.nvidia.com/compute/cuda/8.0/secure/Prod2/patches/2/cuda_8.0.61.2_windows.exe](http://developer2.download.nvidia.com/compute/cuda/8.0/secure/Prod2/patches/2/cuda_8.0.61.2_windows.exe)
2. cudnn-8.0-windows7-x64-v6.0.zip [http://developer2.download.nvidia.com/compute/machine-learning/cudnn/secure/v6/prod/8.0_20170427/cudnn-8.0-windows7-x64-v6.0.zip](http://developer2.download.nvidia.com/compute/machine-learning/cudnn/secure/v6/prod/8.0_20170427/cudnn-8.0-windows7-x64-v6.0.zip)：
和上面的原因一样，请匹配6.0版本。
3. tensorflow [https://github.com/tensorflow/tensorflow](https://github.com/tensorflow/tensorflow)：
我之所以给出github的地址是因为tensorflow团队在github上每天12：34都有一次build，并且github不受“种种”网络因素的影响。在tensorflow找到python3.6对应gpu版本[build history](http://ci.tensorflow.org/view/tf-nightly/job/tf-nightly-windows/M=windows-gpu,PY=36/)，找到whl文件地址。
4. Anaconda3-5.0.0-Windows-x86_64.exe [https://www.anaconda.com/download/](https://www.anaconda.com/download/)：
anaconda有一个最大的好处就是安装各种python库比较方便。
# 安装包关系 #
anaconda相当于tensorflow运行的容器。anaconda可以创建多个“盒子”（environment），每个盒子中的环境互不干扰，所以使用anaconda可以同时安装python3.5/3.6，tensorflow1.3/1.5。
cuda和cudnn是tensorflow调用gpu所需要的库。也就是说tensorflow必须通过cuda和cudnn来调用电脑的gpu。
![图片描述][1]
#安装#
### 安装anaconda、anaconda、cuda、cudnn ###
1. anaconda、cuda、cudnn安装即可。在安装过程中会自动配置环境变量。
2. 不过需要手动将cuda的development目录配置到`CUDA_HOME`中。
3. 将cudnn解压后，把文件复制到cuda对应目录。
### 安装tensorflow ###
1. 启动anaconda,点击**environments**（环境），点击**create**（新建），命名`tensorflow-gpu`，选取`3.6`版本。
2. 点击tensorflow-gpu启动**Open Terminal**，输入`activate tensorflow-gpu`。这时，anaconda下**名字叫做tensorflow-gpu的环境**已经启动了。下面我们才真正开始安装tensorflow。
3. 输入
`pip install --ignore-installed --upgrade http://ci.tensorflow.org/view/tf-nightly/job/tf-nightly-windows/M=windows-gpu,PY=36/lastSuccessfulBuild/artifact/cmake_build/tf_python/dist/tf_nightly_gpu-1.5.0.dev20171024-cp36-cp36m-win_amd64.whl`
稍等片刻tensorflow就安装成功了。
# 测试tensorflow环境 #
1. 点击anaconda下我们创建的环境`tensorflow-gpu`启动**Open With Python**
2. 输入`import tensorflow`如果不报错就说明安装成功了。
# 常见错误 #
1. ImportError: DLL load failed: 找不到指定的模块。  这个错误通常是cuda或者cudnn与tensorflow的版本对应错误。推荐下载cuda8+cudnn6。
当然，随着时间推移这些版本会被淘汰，但本教程依然适用。在github上找到tensorflow项目，在项目内搜索`TF_CUDA_VERSION`和`TF_CUDNN_VERSION`会看到当前tensorflow对应的是哪个cuda和cudnn版本。
2. 其他错误。
除1的错误外我暂时没碰到其他错误，如果出现排错的思路是，确认版本-->确认库是否包含在path中。如果依然无法解决，可以加我微信`takeurhand`讨论。
# 运行mnist例子 #
1. mnist例子运行需要安装matplotlib库，这时候anaconda的方便之处就得以体现了。点击anaconda下`tensorflow-gpu`环境，再右侧搜索matplotlib，勾选并点击apply即可。
2. 下载github上mnist教程例子[https://github.com/martin-gorner/tensorflow-mnist-tutorial](https://github.com/martin-gorner/tensorflow-mnist-tutorial)，并解压。
3. 启动anaconda下`tensorflow-gpu`环境**Open Terminal**，输入`activate tensorflow-gpu`，cd到步骤2解压目录。
4. 执行`python mnist_xx.py`
![图片描述][2]


  [1]: /assets/images/20171024/1.jpg
  [2]: /assets/images/20171024/2.png
