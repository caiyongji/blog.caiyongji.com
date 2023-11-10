---
title: 最佳实践：五分钟打造你自己的GPT
tags:
- GPT
- ChatGPT
- OpenAI
---

前几天OpenAI的My GPTs栏目还是灰色的，就在今天已经开放使用了。有幸第一时间体验了一把生成自己的GPT，效果着实惊艳！！！我打造的GPT模型我会放到文章末尾，大家感兴趣也可以自己体验一下。
![Alt text](/assets/images/20231110/image.png)

# 打造自己的GPT模型
点击Create a GPT，可以进入到下面这个界面，左侧是一个GPT Builder的对话框，右边是你随时更改GPT配置后的预览。
![Alt text](/assets/images/20231110/image-1.png)

点进去Configure配置，可以看到具体的头像、名称、描述、说明、对话引导、知识库、可选择的模型能力（是否打开联网、Dall·E图像生成、代码解释器）以及Actions这个是模型调用API的能力。
![Alt text](/assets/images/20231110/img5.png)

我上传了我所有的mardown文章内容，然后告诉模型，我要一个博客写作GPT帮我写技术文章。
# GPT builder
然后，我让GPT Builder给我一些建议，他给我罗列了一些，他告诉我可以针对我的需求加入合适的用户交互，要突出技术专长，要经常更新（做不到），可以加入我自己的头像，最重要的是，他说他可以fine-tuning模型。这点我非常惊讶，因为fine-tuning是真正的让GPT背后的模型适配你的数据，也就是我创造的GPT需求，它需要根据数据反向传播影响模型参数的，而fine-tuning的成本也是不便宜的。  
我试了试，它真的根据我的需求调整了模型。
![Alt text](/assets/images/20231110/image-2.png)

# 发布
发布时可以选择公开发布，它提示你公开的GPT模型可能会出现在未来GPT商店里，足以看出openAI对整个模型生态的野心。

![Alt text](/assets/images/20231110/image-3.png)
# 实践
我让他根据我以往的写作风格写一篇技术文章，它选择了[《深入理解Python异步编程：从原理到实践》](https://blog.caiyongji.com/2023/11/10/gpt-sample.html)我已经放到了我的个人博客上，大家可以自己看看写的怎么样。  
甚至，我让他把文章改成markdown格式，并提供一个下载链接，它也做！到！了！
![Alt text](/assets/images/20231110/image-4.png)

# CaiBlogGPT
总的来说，这意味着老板们想要啥可以自己打造了，哪有什么技术壁垒。我的理解是，谁有数据，谁就有模型能力，技术层面，已经是傻瓜式了。 机器学习已经毫无意义，打造自己的knowledge base（知识库）吧。那么谁有整合这些数据的能力呢？这个赢家通吃，强者恒强的世界呀！  
这就是我生成自己的GPT的体验。大家可以试试我用5分钟打造的gpt： [https://chat.openai.com/g/g-ARKi8yoKs-caibloggpt](https://chat.openai.com/g/g-ARKi8yoKs-caibloggpt)。  

我从来只原创，也不水文章（咳咳），所以几年没更新。我早已财务自由（并没有），下次再见。  