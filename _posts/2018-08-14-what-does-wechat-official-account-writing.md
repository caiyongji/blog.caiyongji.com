---
title: 那些10w+的公众号都在写什么？
tags:
- 10w+
- 公众号
---
> 出于好奇，那些10w+的公众号都写了些什么，于是我写了几个脚本爬取了各行业Top的公众号文章，进行了关键词统计。

抓取数据、分析用到了3中语言：Node.js，Java，Python。废话不多说，直接上代码。

# 1(NODEJS)

puppeteer模拟登陆，抓取微信公众号链接：
````javascript
/**
* load wechat article urls on newrank.cn
**/
const puppeteer = require('puppeteer');
//emulate iphone
const userAgent = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.87 Safari/537.36';
const workPath = './newrank_cn1111';
const fs = require("fs");
const userName = "公众号";
const ppwwdd = "caiyongji";
if (!fs.existsSync(workPath)) {
        fs.mkdirSync(workPath)
}
const loginUrl = 'https://www.newrank.cn/public/login/login.html?back=https%3A//www.newrank.cn/';

const monthlyRankUrl = "https://www.newrank.cn/public/info/list.html?period=month&type=data";

const detailUrl = "https://www.newrank.cn/public/info/detail.html?account=";

(async () => {

    const browser = await puppeteer.launch({headless: false});//set headless: true will hide chromium UI
    const page = await browser.newPage();
    await page.setUserAgent(userAgent);
    await page.setViewport({width:1920, height:1000});
    await page.setRequestInterception(true);

    //filter to block images
    page.on('request', request => {
    if (request.resourceType() === 'image')
      request.abort();
    else
      request.continue();
    });
    await page.goto(loginUrl);
    //login
    await loginOperate();
    //await page.close();

    await processMonthlyRank('.wx-right-type-list-spe a[icon=ss]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=mgs]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=cf]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=kj]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=cy]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=qc]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=ls]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=zc]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=jy]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=xs]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=zw]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=qy]');

    await processMonthlyRank('.wx-right-type-list-spe a[icon=wh]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=bk]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=jk]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=shs]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=ms]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=sj]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=lx]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=ym]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=qg]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=ty]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=mt]');
    await processMonthlyRank('.wx-right-type-list-spe a[icon=zs]');

    await processMonthlyRank('#wx_month_all');




    async function loginOperate(){
        try{
            await page.click('div[data-type=pwd]');
        }catch(err){
            console.log('login#1');
        }

        try{
            await page.type('#account_input',userName);
            await page.type('#password_input',ppwwdd);
        }catch(err){
            console.log('login#2');
        }

        try{
            await page.click('#pwd_confirm');
        }catch(err){
            console.log('login#3');
        }

    }

    async function processMonthlyRank(btn){
        const tab = await browser.newPage();
        await tab.setUserAgent(userAgent);
        await tab.setViewport({width:1920, height:1000});
        await tab.setRequestInterception(true);

        //filter to block images
        tab.on('request', request => {
        if (request.resourceType() === 'image')
          request.abort();
        else
          request.continue();
        });
        await tab.goto(monthlyRankUrl);
        try{
            await tab.click(btn);
        }catch(err){
            console.log('processMonthlyRank#1');
        }
        let fileName = await tab.evaluate(function(param){
            return document.querySelector(param).innerHTML;
        },btn);
        console.log('-------------------------'+fileName+'-------------------------');
        await scrollWait(tab);
        await waitSecond(tab);

        const sel = '.wx_main tr';
        const texts = await tab.evaluate((sel) => {
        let elements = Array.from(document.querySelectorAll(sel));
            let txt = elements.map(element => {
                return element.innerText
            })
            return txt;
        }, sel);
        console.log('total rows: '+texts.length);
        let contents='记录条数'+(texts.length-1)+'\n\n';
        texts.forEach(function(c,index){
            if(index>0){
                contents+=c+'\n\n';
            }
        });

        const fs = require("fs");
        fs.writeFileSync(workPath+'/'+fileName+'.txt',contents);
        console.log(fileName + " has been extracted to local.");

        const idSel = '.wx_main tr a[href^="detail.html"]';
        const ids = await tab.evaluate((idSel) => {
        let elements = Array.from(document.querySelectorAll(idSel));
            let txt = elements.map(element => {
                return element.innerText
            })
            return txt;
        }, idSel);
        let idContents='';
        let w_name;
        let flag =true;
        /*ids.forEach(async function(id,index){
            if(index%2!=0){
                idContents+=id+'\n';
                await getDetail(fileName,w_name,id);
                w_name =null;
            }else{
                w_name=id;
            }
        });*/
        await (async ()=>{
            for(let i=0;i<ids.length;i++){
                if(i%2!=0){
                idContents+=ids[i]+'\n';
                await getDetail(fileName,w_name,ids[i]);
                w_name =null;
            }else{
                w_name=ids[i];
            }
            }
        })();
        let idFile = 'id_'+fileName;
        fs.writeFileSync(workPath+'/'+idFile+'.txt',idContents);
        console.log(idFile + " has been extracted to local.");
        await tab.close();
    }

    async function scrollWait(p, n){
        if(n==null) n=5;
        for(let i= 0; i<n;i++){
            try{
                await p.evaluate(()=>window.scrollTo(0, document.body.scrollHeight));
                await p.waitForNavigation({timeout:500,waitUntil: ['networkidle0']});
            }catch(err){
                console.log('scroll to bottom and then wait 500 ms.');
            }
        }
    }

    async function waitSecond(p){
        try{
            await p.waitForNavigation({timeout:2000,waitUntil: ['networkidle0']});
        }catch(err){
            //console.log('wait 1 sec.');
        }
    }

    async function getDetail(cat,name,id){
        const tab = await browser.newPage();
        await tab.setUserAgent(userAgent);
        await tab.setViewport({width:1920, height:1000});
        await tab.setRequestInterception(true);

        //filter to block images
        tab.on('request', request => {
        if (request.resourceType() === 'image')
          request.abort();
        else
          request.continue();
        });
        await tab.goto(detailUrl+id);
        await waitSecond(tab);
        const sel = '#info_detail_article_top li .title a';
        const hrefs = await tab.evaluate((sel) => {
            let elements = Array.from(document.querySelectorAll(sel));
            let links = elements.map(element => {
                return element.href
            })
            return links;
        }, sel);
        let urlList='';
        hrefs.forEach(function(href,index){
            urlList+=href+"\n";
        });
        const fs = require("fs");
        if (!fs.existsSync(workPath+'/'+cat)) {
            fs.mkdirSync(workPath+'/'+cat)
        }
        fs.writeFileSync(workPath+'/'+cat+'/'+id+'_top_'+name+'.txt',urlList);

        const sel1 = '#info_detail_article_lastest li .title a';
        const hrefs1 = await tab.evaluate((sel1) => {
            let elements = Array.from(document.querySelectorAll(sel1));
            let links = elements.map(element => {
                return element.href
            })
            return links;
        }, sel1);
        let urlList1='';
        hrefs1.forEach(function(href,index){
            urlList1+=href+"\n";
        });
        fs.writeFileSync(workPath+'/'+cat+'/'+id+'_lastest_'+name+'.txt',urlList1);
        console.log(id+' '+name+' has been extracted to local.');
        await tab.close();
    }

})();

````
# 2（JAVA）

Jsoup抓取微信文章文本：

```java
package com;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.util.Arrays;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ThreadLocalRandom;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;

public class WeChatUrls extends Thread {
    private File catFile;
    final static Integer ThreadNum = 1;
    final String ERROR = "ERROR";
    private final static String USER_AGENT = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.87 Safari/537.36";
    private final static String WORK_FOLDER = "T:\\Developer\\puppeteerTestCase\\newrank_cn_articles";
    private final static String READ_URLS_FOLDER = "T:\\Developer\\puppeteerTestCase\\newrank_cn";

    public WeChatUrls(File cat) {
        this.catFile = cat;
    }

    private String getUrlProxyContent(String url) {
        String body = ERROR;
        try {
            Document doc = Jsoup.connect(url).userAgent(USER_AGENT).get();
            if (doc.select("body") != null) {
                body = doc.select("body").text();
            }
        } catch (IOException e) {
            System.out.println("ERROR URL: " + url);
            e.printStackTrace();
        }

        return body;
    }

    private void write(String content, String fileName) {
        File f = new File(fileName);
        FileWriter fw = null;
        BufferedWriter bw = null;
        try {
            if (!f.exists()) {
                f.getParentFile().mkdirs();
                f.createNewFile();
            }
//             fw = new FileWriter(f.getAbsoluteFile(), true); // true表示可以追加新内容
            fw = new FileWriter(f.getAbsoluteFile()); // 表示不追加
            bw = new BufferedWriter(fw);
            bw.write(content);
            bw.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) throws Exception {
        File baseFolder = new File(READ_URLS_FOLDER);
        File[] cataFiles = baseFolder.listFiles();
        ExecutorService service = Executors.newFixedThreadPool(ThreadNum);
        Arrays.asList(cataFiles).stream().forEach(catFile -> {
            if (catFile.isFile() && catFile.getName().startsWith("id")) {
                service.execute(new WeChatUrls(catFile));
            }
        });
        service.shutdown();
    }

    private void process() {
//        Set<String> redoSet = new HashSet<>();
        String catagory = catFile.getName().split("\\.")[0].split("_")[1];
        File urlFolder = new File(READ_URLS_FOLDER + "\\" + catagory);
        File[] urlFiles = urlFolder.listFiles();
        if (urlFiles != null) {
            Arrays.asList(urlFiles).stream().forEach(urlFile -> {
                try {
                    BufferedReader reader = new BufferedReader(new FileReader(catFile));
                    String wechatId = null;
                    int countLatest = 1;
                    int countTop = 1;
                    while ((wechatId = reader.readLine()) != null) {
                        if (urlFile.getName().startsWith(wechatId)) {
                            String wechatName = urlFile.getName().split("\\.")[0].split("_")[2];
//                            if (urlFile.length() == 0) {
//                                redoSet.add("\"" + catagory + "\",\"" + wechatName + "\",\"" + wechatId + "\"");
//                            }
                            BufferedReader r = new BufferedReader(new FileReader(urlFile));
                            String wechatUrl = null;
                            while ((wechatUrl = r.readLine()) != null) {
                                String writePath = WORK_FOLDER + "\\" + catagory + "\\"
                                        + (urlFile.getName().contains("top") ? "top" : "latest") + "\\" + wechatId
                                        + "_" + wechatName + "_"
                                        + (urlFile.getName().contains("top") ? countTop++ : countLatest++)+".txt";
                                String content = getUrlProxyContent(wechatUrl);
                                write(content, writePath);
                                System.out.println(writePath);
                                Thread.sleep(ThreadLocalRandom.current().nextInt(500, 3000));
                            }
                            r.close();
                        }
                    }
                    reader.close();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            });
        }
//        redoSet.stream().forEach(System.out::println);

    }

    @Override
    public void run() {
        process();
    }
}

```
# 3（PYTHON）

wordcloud生成词云：

```python
# -*- coding: utf-8 -*-
import json
import random
import time
import os
from pyecharts import Bar,Geo,Line,Overlap
import jieba
from scipy.misc import imread
from wordcloud import WordCloud, ImageColorGenerator
import matplotlib.pyplot as plt
from collections import Counter
os.chdir('T:/Developer/puppeteerTestCase/newrank_cn_articles')

stopWords = ['微信','二维码','二维','扫一','一扫','公众','赞赏','转账','关注','打开','阅读','图片','关闭','取消','程序']

def proc(folder, type):
    fileLines = []
    rootdir = './'+folder+'/'+type
    list = os.listdir(rootdir)
    for i in range(0,len(list)):
            path = os.path.join(rootdir,list[i])
            if os.path.isfile(path):
                    try:
                        fo = open(path, 'r+')
                        fileLines += fo.readlines()
                    except:
                        print('error while processing file: ' + path)

    _str =  ' '.join(fileLines)
    words_list = []
    word_generator = jieba.cut_for_search(_str)
    for word in word_generator:
        words_list.append(word)
    words_list = [k for k in words_list if len(k)>1 and k not in stopWords]
    back_color = imread('back.jpg')
    wc = WordCloud(background_color='white',
                   max_words=2000,
                   mask=back_color,
                   max_font_size=300,
                   font_path="C:/Windows/Fonts/msyh.ttc",
                   random_state=42
                   )
    _count = Counter(words_list)
    wc.generate_from_frequencies(_count)
    image_colors = ImageColorGenerator(back_color)
    wc.recolor(color_func=image_colors)
    #plt.figure()
    #plt.imshow(wc.recolor(color_func=image_colors))
    #plt.axis('off')

    # The pil way (if you don't have matplotlib)
    image = wc.to_image()
    image.show()
    jpgFile = './'+type+'_'+folder+'.jpg'
    image.save(jpgFile)
    print('image File saved:' + jpgFile)





basedir = './'
baselist = os.listdir(basedir)
for l in range(0,len(baselist)):
        p = os.path.join(basedir,baselist[l])
        if os.path.isdir(p):
                proc(os.path.basename(p), 'top')

```

# 4 结果

词云结果涉及23个维度，得出结果如下：

TOP500公众号文章

![](/assets/images/20180814/1.webp)

创业

![](/assets/images/20180814/2.webp)

健康

![](/assets/images/20180814/3.webp)

教育

![](/assets/images/20180814/4.webp)

乐活

![](/assets/images/20180814/5.webp)

企业

![](/assets/images/20180814/6.webp)

情感

![](/assets/images/20180814/7.webp)

体育娱乐

![](/assets/images/20180814/8.webp)

文化

![](/assets/images/20180814/9.webp)

文摘

![](/assets/images/20180814/10.webp)

幽默

![](/assets/images/20180814/11.webp)

政务

![](/assets/images/20180814/12.webp)

旅行

![](/assets/images/20180814/13.webp)

时事

![](/assets/images/20180814/14.webp)

时尚

![](/assets/images/20180814/15.webp)

民生

![](/assets/images/20180814/16.webp)

汽车

![](/assets/images/20180814/17.webp)

百科

![](/assets/images/20180814/18.webp)

科技

![](/assets/images/20180814/19.webp)

美体

![](/assets/images/20180814/20.webp)

美食

![](/assets/images/20180814/21.webp)

职场

![](/assets/images/20180814/22.webp)


财富

![](/assets/images/20180814/23.webp)


#最后
数据集已开源。
关注公众号 caiyongji 回复 10w_article 获取代码以及数据。
或github:https://github.com/caiyongji/wechat-ranking
