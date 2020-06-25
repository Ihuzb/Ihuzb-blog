---
title: W4J音乐热评一Puppeteer爬虫初体验
date: 2019-12-01 15:34:16
tags:
- JavaScript
- Node.js
- W4J
- 爬虫
- Puppeteer
categories:
- JavaScript
id: kS4LfLOW08
---
## Puppeteer介绍

- 通俗理解`Puppeteer`就是一个`Nodejs`库，我们可以创建一个可被我们控制的`Chmod`浏览器，调用`API`可以操作`WEB`，,它最大的特点就是它的操作Dom可以完全在内存中进行模拟既在V8引擎中处理而不打开浏览器(也是可打开浏览器，配置即可)。
- 简直就是`JS`开发爬虫的一大福音。
- 更多详细信息请点击这里[Puppeteer](https://zhaoqize.github.io/puppeteer-api-zh_CN/#/)

## Puppeteer用处

- 利用网页生成PDF、图片
- 可以从网站抓取内容
- 自动化表单提交、UI测试、键盘输入等
- 捕获站点的时间线，以便追踪你的网站，帮助分析网站性能问题

## Puppeteer初体验

- 我们以爬取音乐《囍》评论为例练练手。

### 安装Puppeteer

```js
# 安装命令
npm i puppeteer --save

# 会有一个比较长的下载过程稍等片刻
Downloading Chromium r756035 - 144.6 Mb [=                   ] 6% 126.6s

# 安装完成
+ puppeteer@3.3.0
```

### 使用Puppeteer

```js
const puppeteer = require('puppeteer');
//puppeteer涉及很多await异步操作，所以我们需要再async自执行函数下执行
(async () => {
    //创建浏览器示例
    const browser = await puppeteer.launch({
        //设置超时时间
        timeout: 15000,
        //如果是访问https页面 此属性会忽略https错误
        ignoreHTTPSErrors: true,
        // 打开开发者工具, 当此值为true时, headless总为false
        devtools: true,
        //false 无头模式 打开浏览器 ， true 不打开浏览器
        headless: false,
        //优化Chromium启动项 查看：https://peter.sh/experiments/chromium-command-line-switches/
        args: [
            '-–disable-dev-shm-usage',
            '-–disable-setuid-sandbox',
            '-–no-first-run',
            '--no-sandbox',
            '-–no-zygote',
            '-–single-process'
        ]
    });
    //打开一个新的标签页
    const page = await browser.newPage();

    //跳转到指定地址
    await page.goto('https://music.163.com/#/song?id=1303289043');

    //至此我们就创建了一个浏览器，并打开指定页面。
    //我们分析页面得知，整个页面是被放在一个`name`为`contentFrame`的`iframe`里。

    //获取存放页面内容的iframe
    const iframe = await page.frames().find(f => f.name() === 'contentFrame');

    //获取到评论所在的父div
    const musicComment = await iframe.$('.cmmts.j-flag');

    //在浏览器中执行函数，相当于在控制台中执行函数
    //在evaluate中写的console.log都将会在开发者工具中显示
    let commentList = await iframe.evaluate((e) => {
        //这次不罗里吧嗦直接使用评论内容所在的元素类名获取评论内容，使用Array.from便于接下来map操作
        let comment = Array.from(e.getElementsByClassName('cnt f-brk'));

        //可以打印一下，在开发者工具中查看
        console.log(comment);

        //数组内是一个个元素对象，直接获取innerText即可
        return comment.map(item => item.innerText);
    }, musicComment);
    //commentList变量中就是爬取的内容
    console.log(commentList)

    //爬取完成关闭浏览器
    await browser.close();
})()
```

```js
//爬取的评论内容
[
  'Liam_BZ：唢呐倒是个神奇的东西\n中国人的红白喜事，从生到死\n一根铜管全能吹出来',
  '今天仙女味儿：新郎爱我\n王二狗也爱我\n可是新郎再爱我也没有让我和王二狗走\n王二狗再爱我 也没有来劫婚\n这就是，他们说的爱',
  '也想不羁尽自由：双喜才是囍 怕我只是喜',
  '别听别人乱讲你长得很可爱：门上贴囍 身着红衣 心里有你',......]
```
