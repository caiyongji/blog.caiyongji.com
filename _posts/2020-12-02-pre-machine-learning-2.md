---
title: 前置机器学习（二）：30分钟掌握常用Jupyter Notebook用法
tags:
- Pre-ML
- 前置机器学习
- Jupyter Notebook
- Jupyter Notebook 快捷键
- LaTeX
---
>相较于Pycharm执行py文件来说，Jupyter Notebook可保存执行过程，添加图表、注释等富文本说明的功能，使其对机器学习的开发者格外友好。


本文包含机器学习环境安装，Jupyter Notebook常见用法：常见使用、命令、快捷键等。进阶用法：LaTeX数学公式、魔法命令等。

本文收录于[机器学习前置教程系列](https://mp.weixin.qq.com/mp/appmsgalbum?action=getalbum&__biz=MzUxMjU4NjI4MQ==&scene=1&album_id=1627166768236412929&count=3#wechat_redirect)。

# 一、机器学习环境安装

网上安装机器学习环境的资料非常多，我没有必要过分详细介绍。此处推荐我个人的安装方式Miniconda+清华镜像，因为使用国内镜像所以非常快。

## 1. Miniconda
Miniconda 是Anaconda的**最小**安装版。只包含`conda`和`Python`以及它们依赖的少量软件包，包括`pip`, `zlib`等。  
[Miniconda下载地址：https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html)


## 2. pip install

下载安装常用软件包，`-i` 参数指定清华镜像下载。
**注意**：本例安装tensorflow CPU版本，如需安装tensorflow GPU版本，替换`tensorflow-cpu`为`tensorflow`。
GPU版TensorFlow需安装CUDA和cuDNN，详情见[TensorFlow中文官网-GPU支持: https://tensorflow.google.cn/install/gpu](https://tensorflow.google.cn/install/gpu)。

```
pip install --upgrade pip
```
```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple tensorflow-cpu tensorflow-datasets tf-agents matplotlib pandas scikit-learn scikit-image ipython jupyter keras gym lxml xlrd openpyxl sqlalchemy
```

## 3. Colaboratory[可选]
对于有*外网条件*的人来说，Colaboratory可以作为上述配置的替代品。
Colaboratory是谷歌开发的在浏览器上运行Python的工具，其支持GPU和TPU。使用体验与Jupyter Notebook一致，但更方便。以我个人使用为例，它免费赠送12.72GB内存和107.77GB的硬盘。简单来说，它比一般的个人电脑要快。Colab支持付费版，性能更高。
[Colaboratory官方地址：https://colab.research.google.com/](https://colab.research.google.com/)


# 二、Jupyter Notebook

打开Miniconda/Anaconda命令行，输入启动命令启动Jupyter。

```
jupyter Notebook
```

如下图，进入Jupyter启动页，然后点击`New --> Python3` 新建Notebook。现在我们正式进入Jupyter Notebook的世界。


![image](/assets/images/20201202/new-notebook.gif)


## 1. 常见用法


### 1.1 打印
键入命令，按Ctrl+Enter执行单元格。
```
print("2020, 戴好口罩！")
```
执行结果如图所示
![image](/assets/images/20201202/hello-world.png)
### 1.2 新建单元格，自动补全
按B（Below）键，在单元格下方新建单元格。

输入下方代码，按`Shift+Enter`执行单元格。试试在输入`np.lins`时按Tab键，Jupyter将自动补全代码。关于`linspace`方法，我们将在接下来讲解Numpy的文章中说明。
```
import numpy as np
myarray = np.linspace(0,10,101)
```
`Shift+Enter`将执行选中单元格，并选择下方单元格。如下方无单元格则新建单元格。

![image](/assets/images/20201202/numpy.png)


### 1.3 代码提示
输入`myarray`，按`Alt+Enter`执行选中单元格，并在下方插入新的单元格。我们将光标放在代码后方，按`Shift+Tab`查看代码提示。按**两次**`Shift+Tab`可查看代码详情。
```
myarray
```
如图显示，myarray是ndarray类型的数组。
![image](/assets/images/20201202/tootip.png)


### 1.4 Markdown 

选中myarray单元格，按 `A`  （Above）在myarray上方插入单元格，再按 `M` 将单元格格式转换为Markdown格式。
```
## markdown
在此处点击**Shift+Tab**可查看myarray数据类型。
```
执行得到如下显示。
![image](/assets/images/20201202/markdown.png)

### 1.5 序号说明

单元格前面的序号表示单元格执行的次序。**其中空[ ]为从来没执行过。[\*]为尚未执行到。**
![image](/assets/images/20201202/order.png)



### 1.6 Jupyter两种模式及全部快捷键
#### 1.6.1 命令模式（按ESC进入）

Jupyter常用命令模式快捷键：

| 快捷键 | 操作 |
| --- | --- |
| A | 在上方插入单元格 |
| B | 在下面插入单元格 |
| D,D | 删除选定的单元格 |
| Shift-Enter | 运行单元格，在下面选择 |
| Ctrl-Enter | 运行选定的单元格 |
| Alt-Enter| 运行单元格并在下面插入|
| Y | 将单元格更改为代码格式 |
| M | 将单元格更改为Markdown格式 |


#### 1.6.2 编辑模式（按Enter进入）

Jupyter常用编辑模式快捷键：

| 快捷键 | 操作 |
| --- | --- |
| Tab | 代码补全或缩进 |
| Shift-Tab | 代码提示 |
| Ctrl-D | 删除整行 |
| Shift-Enter | 运行单元格，在下面选择 |
| Ctrl-Enter | 运行选定的单元格 |
| Alt-Enter| 运行单元格并在下面插入|

#### 1.6.3 Jupyter Notebook快捷键大全
进入命令模式，按 `H` 查看所有Jupyter Notebook快捷键。
![image](/assets/images/20201202/shotcut.png)

## 2. 进阶用法

### 2.1 LaTeX数学公式支持

Jupyter Notebook 支持LaTex数学公式。新建Markdown代码格式单元格。

输入下方文本将现实LaTeX公式，这对我们学习机器学习记数学公式非常有帮助。
```
### variance 方差公式
$$s^2 = \frac{\sum_{i=1}^{n}{(x_i -x)^2}}{n}$$
(其中`x`为均值)
```
![image](/assets/images/20201202/latex.png)

试试执行下面的代码，看看会显示什么？
```
\begin{equation*}
\left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)
\end{equation*}
```


更多LaTeX的用法，我会在接下来的文章中详细介绍，还请您多多关注我。

### 2.2 魔法命令
输入`%lsmagic`查看所有可用魔法命令。
```
%lsmagic
```
![image](/assets/images/20201202/magic.png)

其中比较常见的如 %pwd, %timeit等。
![image](/assets/images/20201202/magic-sample.png)


## 3. 更多

Jupyter Notebook 官方文档： [https://jupyter-Notebook.readthedocs.io/en/stable/examples/Notebook/examples_index.html](https://jupyter-Notebook.readthedocs.io/en/stable/examples/Notebook/examples_index.html)

